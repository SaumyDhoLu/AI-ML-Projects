# MRI Cross-Contrast Synthesis using CycleGAN

## Project Overview

This project implements an unpaired image-to-image translation system using a **Cycle-Consistent Generative Adversarial Network (CycleGAN)** to synthesize one MRI contrast from another.

The primary task is:

- T1-weighted MRI → T2-weighted MRI
- T2-weighted MRI → T1-weighted MRI

The project explores whether a CycleGAN can learn contrast/style translation while preserving the underlying anatomical structure of brain MRI images.

Unlike paired image translation methods, CycleGAN does not require one-to-one corresponding T1/T2 image pairs during training, making it suitable for datasets where images from the two domains are available independently.

---

## Problem Statement

MRI examinations can contain multiple imaging contrasts, with different contrasts highlighting different tissue characteristics and anatomical details.

When a particular contrast is unavailable, image-to-image translation can potentially be used to synthesize an alternative contrast from an available scan.

The objective of this project is to investigate a deep generative approach for:

> **Translating between T1-weighted and T2-weighted MRI domains while maintaining the structural information of the original brain image.**

This project is an experimental deep-learning study and is **not intended for clinical diagnosis or clinical deployment**.

---

## Objectives

1. Build a CycleGAN-based unpaired MRI translation system.
2. Translate T1-weighted MRI images into the T2 domain.
3. Translate T2-weighted MRI images into the T1 domain.
4. Preserve structural information through cycle consistency.
5. Implement custom Instance Normalization.
6. Build U-Net-style generators with skip connections.
7. Build PatchGAN discriminators.
8. Train the complete four-network CycleGAN architecture.
9. Visualize translation quality throughout training.
10. Evaluate cycle reconstruction using SSIM.
11. Save and reload trained models for inference.
12. Perform additional qualitative inference on NIFTI-format MRI data from the IXI dataset.

---

## Dataset

### Initial Training Dataset

The notebook uses a small collection of grayscale MRI images:

| Domain | Number of Images |
|---|---:|
| T1 | 43 |
| T2 | 46 |

The different image counts reflect the **unpaired** nature of the training data.

Original sample dimensions:

```
181 × 217
```

Images are converted into a standardized representation of:

```
256 × 256 × 1
```

### External Inference Dataset

The notebook also contains an inference pipeline for MRI scans from the IXI dataset in NIFTI format.

The pipeline:

- Loads 3D NIFTI files.
- Extracts the center axial slice.
- Normalizes the slice to 8-bit intensity.
- Converts it to a PIL image.
- Resizes/pads it to the model input format.
- Normalizes it to [-1, 1].
- Runs both trained generators.
- Saves visual comparison results.

The notebook reports processing 581 T1 NIFTI files during this inference experiment.

---

## Why CycleGAN?

Traditional supervised image translation approaches generally require aligned source-target image pairs.

In this project, the T1 and T2 training images are treated as separate domains rather than explicitly paired examples.

CycleGAN addresses this using two generators and two discriminators:

```
             T1 Domain
                |
                v
        Generator T1 → T2
                |
                v
             T2 Domain
                |
                v
        Generator T2 → T1
                |
                v
             T1 Domain
```

The reverse cycle encourages the translated image to retain information from the original image.

The same process operates in the opposite direction:

```
T2 → T1 → T2
```

---

## Model Architecture

The implementation consists of four neural networks.

### 1. Generator: T1 → T2

Transforms a T1-weighted MRI image into the T2 domain.

### 2. Generator: T2 → T1

Transforms a T2-weighted MRI image into the T1 domain.

Both generators use a modified U-Net-style encoder-decoder architecture.

**Generator structure**

```
Input: 256 × 256 × 1

        Encoder
          ↓
256 → 128 → 64 → 32 → 16

        Bottleneck
          ↓

        Decoder
          ↓
16 → 32 → 64 → 128 → 256

        Output
          ↓
256 × 256 × 1
```

The encoder contains strided convolution blocks, while the decoder uses transposed convolutions.

Skip connections connect encoder features to corresponding decoder stages to help retain spatial information.

The generator contains approximately 6.17 million trainable parameters.

The final layer uses:

```
tanh
```

to produce values in the [-1, 1] range.

### 3. PatchGAN Discriminator for T1

The T1 discriminator distinguishes:

- real T1 images
- generated T1 images

Instead of producing one classification for the entire image, the PatchGAN discriminator produces a spatial grid of predictions.

### 4. PatchGAN Discriminator for T2

The second discriminator performs the same task for the T2 domain:

- real T2 images
- generated T2 images

This encourages the generators to produce locally realistic image textures and contrast characteristics.

---

## Custom Instance Normalization

A custom `InstanceNormalization` Keras layer is implemented.

Instance Normalization operates independently on each image and channel rather than normalizing across a batch.

This is particularly useful for image-generation and style-transfer architectures where contrast and appearance statistics are important.

The custom layer also includes a `get_config()` implementation so that the trained `.keras` models can be reloaded with the appropriate `custom_objects` mapping.

---

## Image Preprocessing

The preprocessing pipeline standardizes the MRI images before they enter the models.

**Training pipeline**

```
Image
  ↓
Load
  ↓
Resize with padding
  ↓
Resize / jitter to 286 × 286
  ↓
Random crop to 256 × 256
  ↓
Random horizontal flip
  ↓
Normalize to [-1, 1]
  ↓
Model
```

**Inference/test preprocessing**

```
Image
  ↓
Load
  ↓
Resize with padding
  ↓
Normalize to [-1, 1]
  ↓
Model
```

Using `resize_with_pad` helps preserve the original aspect ratio rather than directly stretching the MRI image.

---

## Data Augmentation

The training pipeline uses a `random_jitter` strategy consisting of:

- resizing to 286 × 286
- random cropping to 256 × 256
- random horizontal flipping

This introduces variation during training and is particularly useful given the small size of the initial dataset.

---

## Loss Functions

The CycleGAN implementation combines three major loss components.

### Adversarial Loss

The generators attempt to produce images that the corresponding discriminator classifies as real.

Binary Cross Entropy with logits is used.

Generator:

```
Fake → should be classified as Real
```

The discriminators learn the opposite:

```
Real → 1
Fake → 0
```

### Cycle-Consistency Loss

Cycle consistency encourages the translation process to preserve information:

```
T1 → T2 → T1
```

and

```
T2 → T1 → T2
```

The implementation uses L1 distance:

```
L1(original, cycled)
```

with:

```
LAMBDA = 10
```

### Identity Loss

Identity loss encourages the generator to avoid unnecessary changes when an image is already from the target domain.

This helps constrain the translation process and reduce unwanted alterations.

---

## Optimization

Each of the four networks has its own Adam optimizer.

Configuration:

```
Optimizer: Adam
Learning Rate: 0.0002
Beta 1: 0.5
Batch Size: 1
Epochs: 200
```

The training step is wrapped using TensorFlow's:

```python
@tf.function
```

to compile the training operation into a TensorFlow graph.

---

## Training Pipeline

Each training iteration performs both translation cycles.

**Cycle 1**

```
Real T1
   ↓
Generator T1 → T2
   ↓
Fake T2
   ↓
Generator T2 → T1
   ↓
Cycled T1
```

**Cycle 2**

```
Real T2
   ↓
Generator T2 → T1
   ↓
Fake T1
   ↓
Generator T1 → T2
   ↓
Cycled T2
```

The discriminators simultaneously evaluate real and generated images.

Four losses are tracked during training:

- Generator T1 → T2 loss
- Generator T2 → T1 loss
- Discriminator T1 loss
- Discriminator T2 loss

---

## Training Visualization

The notebook records generated outputs throughout all 200 epochs.

The saved frames are used to create training-progress GIFs showing how the generated MRI contrast evolves during training.

This provides a visual view of the generative learning process from early noisy outputs to later translated images.

---

## Evaluation

The project uses both qualitative and quantitative evaluation.

### 1. Cycle-Consistency Visualization

The notebook visualizes:

```
Original T1
     ↓
Generated T2
     ↓
Cycled T1
```

and:

```
Original T2
     ↓
Generated T1
     ↓
Cycled T2
```

The purpose is to inspect whether anatomical structure is retained after completing a translation cycle.

### 2. SSIM Evaluation

Structural Similarity Index (SSIM) is calculated between the original image and its cycle-reconstructed image.

Results reported by the notebook:

| Translation Cycle | Average SSIM |
|---|---|
| T1 → T2 → T1 | 0.8858 |
| T2 → T1 → T2 | 0.8387 |

The SSIM calculation is performed after converting the model output from [-1, 1] back to [0, 1].

These values measure cycle reconstruction similarity, not direct accuracy of the generated T2 or T1 image against a paired ground-truth target.

---

## External NIFTI Inference

A separate inference pipeline was implemented to test the saved generators on NIFTI-format MRI data.

**NIFTI processing**

```
3D NIFTI MRI
      ↓
Center axial slice
      ↓
Intensity normalization
      ↓
8-bit image
      ↓
Resize with padding
      ↓
[-1, 1] normalization
      ↓
CycleGAN Generator
```

For each matched T1/T2 scan, the pipeline produces a four-column comparison:

```
Original T1
    |
    v
Generated T2
    |
    v
Ground Truth T2
    |
    v
Generated T1
```

The resulting comparison images are saved for visual inspection.

---

## Model Persistence

The trained networks are saved in Keras format.

```
generator3_t1_to_t2.keras
generator3_t2_to_t1.keras
discriminator3_t1.keras
discriminator3_t2.keras
```

The notebook also demonstrates loading the saved models using:

```python
custom_objects = {
    "InstanceNormalization": InstanceNormalization
}
```

This verifies that the custom normalization layer can be reconstructed when the trained models are loaded.

---

## Project Workflow

```
MRI Dataset
     |
     v
Data Understanding
     |
     v
Image Preprocessing
     |
     +----------------------+
     |                      |
     v                      v
   T1 Domain              T2 Domain
     |                      |
     v                      v
Generator T1→T2       Generator T2→T1
     |                      |
     v                      v
   Fake T2                Fake T1
     |                      |
     +----------+-----------+
                |
                v
        Cycle Reconstruction
                |
                v
       Cycle + Identity Loss
                |
                v
        Adversarial Training
                |
                v
         200 Epoch Training
                |
                v
       Saved Generator Models
                |
                v
     Qualitative + SSIM Analysis
                |
                v
       External NIFTI Inference
```

---

## Project Structure

```
Style_Transfer_using_GAN/
│
├── Style_Transfer_using_GAN_Saumy_DhoLu.ipynb
│
├── generator3_t1_to_t2.keras
├── generator3_t2_to_t1.keras
├── discriminator3_t1.keras
├── discriminator3_t2.keras
│
└── results/
    ├── Inference_results_4/
    ├── gif_frames_t1_to_t2/
    └── gif_frames_t2_to_t1/
```

The exact repository structure may vary depending on which generated artifacts are committed.

---

## Technologies Used

- Python
- TensorFlow
- Keras
- NumPy
- Matplotlib
- PIL / Pillow
- NiBabel
- TensorFlow tf.data
- tqdm
- Jupyter Notebook

---

## Key Technical Concepts Demonstrated

- Generative Adversarial Networks
- CycleGAN
- Unpaired Image-to-Image Translation
- U-Net Architecture
- Encoder-Decoder Networks
- Skip Connections
- PatchGAN
- Instance Normalization
- Adversarial Loss
- Cycle-Consistency Loss
- Identity Loss
- L1 Loss
- SSIM
- MRI Image Processing
- NIFTI Processing
- Data Augmentation
- TensorFlow Custom Layers
- TensorFlow tf.data
- Model Serialization
- Batch Inference
- Qualitative Model Evaluation

---

## Important Evaluation Considerations

This project should be interpreted as an experimental deep-learning implementation rather than a clinically validated MRI synthesis system.

### Small Training Dataset

The initial training data contains only:

- 43 T1 images
- 46 T2 images

This creates a substantial risk of overfitting and limits the conclusions that can be drawn from the experiment.

### No Held-Out Split in the Initial Dataset

The notebook creates both training and evaluation pipelines from the same complete T1 and T2 image lists.

Therefore, the reported SSIM values should be interpreted as cycle-reconstruction measurements on the available dataset, rather than performance on a strictly held-out test set.

### SSIM Interpretation

The reported SSIM values measure similarity between:

```
Original → Generated → Reconstructed
```

They do not directly measure whether the generated T2 image matches a corresponding real T2 image.

### External IXI Evaluation

The IXI experiment provides additional qualitative inference on NIFTI-format scans.

However, the notebook does not provide a comprehensive quantitative evaluation against independently held-out paired ground truth.

### Medical Use

Synthetic MRI generation can potentially alter or introduce image features.

The model should therefore not be interpreted as a clinically validated diagnostic system or as a replacement for medical imaging acquired through standard clinical protocols.

---

## Future Improvements

Several extensions could significantly strengthen the project:

- Train on a substantially larger and more diverse MRI dataset.
- Create a patient-level train/validation/test split.
- Use a genuinely held-out test set for quantitative evaluation.
- Compare generated images against paired ground-truth T2/T1 images where available.
- Add additional image-quality metrics such as PSNR and MAE where appropriate.
- Evaluate anatomical preservation using domain-specific metrics.
- Perform systematic hyperparameter tuning.
- Experiment with alternative generator architectures.
- Investigate stronger MRI-specific normalization and preprocessing.
- Extend the approach from 2D slices to full 3D MRI volumes.
- Evaluate performance across different scanners and acquisition protocols.
- Investigate whether generated contrasts preserve clinically relevant structures before considering any clinical application.

---

## Project Highlights

- Implemented a complete four-network CycleGAN architecture from scratch using TensorFlow/Keras.
- Built two U-Net-style generators with skip connections.
- Built two PatchGAN discriminators.
- Implemented a custom Instance Normalization layer.
- Combined adversarial, cycle-consistency, and identity losses.
- Trained the system for 200 epochs.
- Generated training-progress GIFs.
- Saved and reloaded all four trained models.
- Achieved cycle-reconstruction SSIM values of 0.8858 and 0.8387.
- Built a NIFTI preprocessing and batch-inference pipeline.
- Performed additional qualitative inference using the IXI dataset.

---

## Academic Context

This project was developed as a deep-learning capstone focused on generative modeling and medical image translation.

It demonstrates the practical application of GAN-based architectures to a challenging image-to-image translation problem while covering the complete workflow from preprocessing and model construction to training, evaluation, model persistence, and external inference.

---

## Author

**Saumy DhoLu**

AI/ML Portfolio

GitHub:(https://github.com/SaumyDhoLu)

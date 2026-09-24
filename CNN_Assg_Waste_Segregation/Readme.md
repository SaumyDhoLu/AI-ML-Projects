# Waste Segregation using Convolutional Neural Networks

## Project Overview

This project applies **Deep Learning and Convolutional Neural Networks (CNNs)** to automatically classify waste images into different waste categories.

The objective is to build an image classification system capable of recognizing the type of waste from an input image, and to investigate different CNN architectures and training strategies to improve classification performance.

The project explores CNN architecture design, model selection, image preprocessing, data augmentation, class balancing, and model evaluation.

---

## Problem Statement

Waste management requires effective segregation of waste into appropriate categories before processing or disposal.

Manual waste classification can be time-consuming and inconsistent. An automated image classification system can assist in identifying waste categories from images and potentially support intelligent waste-management workflows.

This project investigates whether a CNN-based image classification model can learn visual patterns from waste images and classify them into predefined categories.

---

## Objectives

The main objectives of the project are:

- Build a CNN-based image classification system for waste segregation.
- Preprocess and standardize waste images for deep learning.
- Experiment with different CNN architectures.
- Compare model performance during training and validation.
- Investigate the effect of image augmentation.
- Address class imbalance during model training.
- Select and save the best-performing trained models.
- Evaluate the final model on unseen data.

---

## Dataset

The project uses an image dataset containing **7 waste categories**.

The images are processed and resized to:

```
128 × 128 × 3
```

where the three channels represent RGB image information.

The dataset is organized into class-specific image directories so that the class label can be inferred from the corresponding directory.

> **Note:** The dataset itself is not included in this repository, as the notebook is designed to work directly with the image dataset used during the project.

---

## Image Preprocessing

The images undergo preprocessing before being provided to the neural network. The main preprocessing steps include:

- Reading image files.
- Converting images into a consistent format.
- Resizing images to 128 × 128 pixels.
- Preparing RGB image tensors.
- Normalizing image pixel values.
- Converting class labels into a format suitable for neural-network training.

Standardizing the image dimensions allows the CNN models to process all images using a consistent input shape.

---

## CNN Modelling Approach

Multiple CNN approaches were explored as part of the project.

### 1. Custom CNN
A convolutional neural network was developed to learn spatial features directly from the waste images. The model uses convolutional and pooling operations to progressively learn visual representations from the input images.

### 2. VGG-Inspired CNN
A deeper CNN architecture inspired by the VGG family was also investigated. The purpose was to evaluate whether increasing the depth and representation capacity of the network could improve classification performance.

### 3. Inception-Style CNN
An Inception-inspired architecture was explored to investigate multiple convolutional feature extraction paths within the network. This allows the model to learn visual patterns at different spatial scales.

---

## Model Training

The project experiments with different training configurations and monitors model performance using training and validation metrics.

Training includes techniques such as:

- Early stopping
- Model checkpointing
- Validation monitoring
- Batch-based training
- Multiple CNN architectures

The best-performing model from the initial CNN experimentation was saved for later evaluation and further experimentation.

---

## Data Augmentation

Image augmentation was investigated to increase variation in the training data.

Augmentation can help expose the CNN to different visual variations of the same underlying waste category and can reduce over-reliance on the exact appearance of individual training images.

The project subsequently evaluates training using augmented images.

---

## Class Balancing

The dataset contains differences in the number of images available for different waste categories. To investigate the effect of class imbalance, a balanced training dataset was also prepared.

The project evaluates training using the augmented and balanced data to determine whether improving class representation can help the classification model.

---

## Model Selection and Saved Models

The project saves trained model artifacts so that the models can be reused without retraining the entire network.

Two trained models are included in the repository:

```
model/
├── Best_CNN_Model.keras
└── best_model_epoch10.keras
```

| Model File | Description |
|---|---|
| `Best_CNN_Model.keras` | Represents the best model selected during the initial CNN experimentation and is subsequently loaded for further experimentation. |
| `best_model_epoch10.keras` | Represents the subsequently trained model saved after the later training stage. |

Both model files are committed to the repository and can therefore be reused directly without reconstructing the models from scratch.

---

## Model Evaluation

The CNN models are evaluated using classification performance on data that was not used for model parameter fitting.

The evaluation process examines the model's ability to correctly classify images across the different waste categories.

The notebook also tracks training and validation performance to identify differences between model learning on the training data and its generalization to validation data.

---

## Results

The project achieved approximately **70% classification accuracy** for the selected CNN model during the documented evaluation.

The experimentation demonstrates the impact that architecture selection, augmentation, and class balancing can have on an image classification problem.

Rather than relying on a single architecture, the project compares multiple CNN approaches and evaluates different training strategies.

---

## Key Learning Outcomes

This project provided practical experience with:

- Image classification
- Convolutional Neural Networks
- Deep learning model development
- Image preprocessing
- CNN architecture experimentation
- VGG-style architectures
- Inception-style architectures
- Data augmentation
- Class balancing
- Early stopping
- Model checkpointing
- Model serialization
- Training and validation analysis
- Image classification evaluation

---

## Technologies Used

**Programming & Data Processing**
- Python
- NumPy
- Pandas

**Deep Learning**
- TensorFlow
- Keras

**Image Processing**
- PIL / Pillow

**Visualization**
- Matplotlib
- Seaborn

**Development Environment**
- Jupyter Notebook

---

## Project Structure

```
CNN_Assg_Waste_Segregation/
│
├── CNN_Assg_Waste_Segregation_Saumy_DhoLu.ipynb
│
├── model/
│   ├── Best_CNN_Model.keras
│   └── best_model_epoch10.keras
│
└── README.md
```

---

## How to Run

### 1. Clone the repository
```bash
git clone https://github.com/SaumyDhoLu/AI-ML-Projects.git
```

### 2. Navigate to the project
```bash
cd AI-ML-Projects/CNN_Assg_Waste_Segregation
```

### 3. Install the required libraries
```bash
pip install tensorflow keras numpy pandas matplotlib seaborn pillow scikit-learn jupyter
```

### 4. Launch Jupyter Notebook
```bash
jupyter notebook
```

Open:
```
CNN_Assg_Waste_Segregation_Saumy_DhoLu.ipynb
```

The notebook contains the complete preprocessing, model development, training, evaluation, and model-saving workflow.

---

## Using the Saved Models

The repository includes the trained `.keras` model files. They can be loaded using TensorFlow/Keras:

```python
from tensorflow.keras.models import load_model

model = load_model("model/Best_CNN_Model.keras")
```

or:

```python
model = load_model("model/best_model_epoch10.keras")
```

> The appropriate image preprocessing and input shape used by the notebook should be applied before passing new images to the model.

---

## Project Highlights

- Developed an end-to-end CNN image classification workflow.
- Worked with a multi-class waste image dataset containing 7 categories.
- Standardized image inputs to 128 × 128 × 3.
- Experimented with multiple CNN architectures.
- Investigated VGG-inspired and Inception-style architectures.
- Applied data augmentation.
- Investigated class balancing.
- Used model-selection and checkpointing techniques.
- Saved trained models as reusable `.keras` artifacts.
- Evaluated model performance on image classification data.

---

## Project Context

This project was developed as part of practical machine learning and deep learning coursework. The project focuses on applying CNN-based computer vision techniques to a real-world-style waste segregation problem.

---

## Author

**Saumy DhoLu**

AI/ML | Machine Learning | Generative AI

GitHub:(https://github.com/SaumyDhoLu)

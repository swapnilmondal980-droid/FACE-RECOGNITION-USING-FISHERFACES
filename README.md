# Face Recognition Using Eigenfaces and Fisherfaces
This project studies face recognition using two classical appearance-based
methods:
- **Eigenfaces**, implemented with Principal Component Analysis (PCA)
- **Fisherfaces**, implemented with Linear Discriminant Analysis (LDA)
The purpose is to compare both approaches and evaluate their recognition
precision using the **AT&T Database of Faces** (also known as the ORL face
dataset). The project focuses on grayscale face images and on the effect of
dimensionality reduction and class discrimination on recognition performance.
## Project objective
A face image contains many pixel values, but not every pixel contributes
equally to identifying a person. The project transforms the original images
into a lower-dimensional feature space and then compares the extracted
features:
1. Prepare and normalize a dataset of grayscale face images.
2. Convert each image into a numerical feature vector.
3. Learn a compact representation with PCA for Eigenfaces.
4. Learn a class-discriminative representation with LDA for Fisherfaces.
5. Recognize unseen images by comparing them with training representations.
6. Compare the precision of the two methods under the same dataset
   conditions.
## Eigenfaces
Eigenfaces use **Principal Component Analysis** to represent faces as
combinations of principal components.
The general process is:
1. Resize every image to the same dimensions.
2. Flatten each image into a one-dimensional vector.
3. Calculate the mean face from the training images.
4. Subtract the mean face from every image.
5. Calculate the principal components of the centered training data.
6. Keep the most important components and project the images into that
   reduced feature space.
7. Compare a test image with the projected training images.
The principal components with the largest variance are called **Eigenfaces**.
They provide a compact representation that can reduce the computational cost
of comparing images.
### Strengths of Eigenfaces
- Simple mathematical foundation
- Relatively inexpensive to train
- Useful for reducing the dimensionality of image data
- Works well when the training and test images have similar conditions
### Limitations of Eigenfaces
- PCA maximizes overall variance, not identity-specific separation.
- Lighting, shadows, background, and facial expression can affect the
  representation.
- A component that describes lighting variation may be more important to PCA
  than a component that distinguishes two people.
## Fisherfaces
Fisherfaces use **Linear Discriminant Analysis** to find a feature space that
separates known people as well as possible.
The general process is:
1. Prepare and flatten the training images.
2. Reduce the image space when necessary using PCA.
3. Use the person labels to calculate within-class and between-class
   variation.
4. Find projections that minimize variation between images of the same person
   and maximize variation between different people.
5. Project training and test images into the Fisherface space.
6. Compare the projected features to determine the recognized identity.
The resulting discriminative components are called **Fisherfaces**.
### Strengths of Fisherfaces
- Uses identity labels during training
- Attempts to separate different people directly
- Can be more robust to some lighting variations than Eigenfaces
- Often provides better class separation when the dataset is properly
  prepared
### Limitations of Fisherfaces
- Requires labeled training data.
- Its behavior depends strongly on the quality and balance of the classes.
- It is sensitive to incorrectly labeled images.
- LDA requires enough samples per person for the class statistics to be
  meaningful.
- A very small or poorly organized dataset can produce unreliable results.
## Eigenfaces versus Fisherfaces
| Property | Eigenfaces | Fisherfaces |
|---|---|---|
| Main technique | PCA | LDA, usually after PCA |
| Uses person labels | No | Yes |
| Main objective | Preserve overall variance | Separate person classes |
| Learns | Principal components | Discriminative components |
| Sensitivity | Sensitive to lighting and global variation | Can reduce some lighting-related effects |
| Dataset requirement | Consistent images | Correct labels and multiple samples per class |
| Best use | Compact feature representation | Identity-focused class separation |
The comparison is meaningful only when both methods use the same image preprocessing, training identities, test identities, and evaluation procedure.
## Dataset
The project is intended to be evaluated with the **AT&T Database of Faces**.
The dataset contains grayscale images grouped by subject. Each subject has
multiple face images captured under different expressions and conditions.
The dataset must be divided into training and testing images before
recognition is evaluated. For example, some images from every subject can be
used for training and the remaining images can be reserved for testing. The
same split must be used for both Eigenfaces and Fisherfaces so that the
comparison is fair.
### Dataset preparation requirements
Before using any other dataset, make sure that it is structured specifically
for this type of experiment:
- Every image must show a face clearly.
- Images must be grayscale or black and white.
- All images must use consistent dimensions, or be resized consistently
  before feature extraction.
- Images must be grouped into identity-specific folders or otherwise have
  reliable identity labels.
- Training and testing images must be separated without mixing the same image
  between the two sets.
- The same preprocessing must be applied to both algorithms.
- The number of images per identity should be reasonably balanced.
- Corrupt, blank, duplicated, or incorrectly labeled images should be removed.
- Lighting, pose, and expression should vary enough to make the evaluation
  meaningful, while remaining suitable for a classical grayscale method.
A dataset that is not organized by identity cannot provide a reliable
Eigenfaces/Fisherfaces comparison because the system cannot determine which
images belong to the same class.
### Recommended directory layout
An equivalent directory layout may be used, but the identity labels must remain
unambiguous:
```text
dataset/
├── subject_01/
│   ├── image_01.pgm
│   ├── image_02.pgm
│   └── ...
├── subject_02/
│   ├── image_01.pgm
│   ├── image_02.pgm
│   └── ...
└── subject_03/
    ├── image_01.pgm
    ├── image_02.pgm
    └── ...
```
The folder name or another explicit label should identify the person. Do not
rely on an arbitrary file order to infer identity.
## Image preprocessing
Classical subspace methods require comparable input images. A typical
preprocessing pipeline is:
1. Load the image in grayscale.
2. Detect or crop the face if the image contains unnecessary background.
3. Resize every image to the same width and height.
4. Convert the image to a consistent numeric type.
5. Flatten the image into a vector.
6. Normalize the data in the same way for training and testing.
Preprocessing must not use information from the test set to train the model.
For example, the mean face and learned projection matrices must be calculated
from the training images only, then applied to the test images.
## Recognition workflow
The complete recognition workflow is:
1. Load the labeled dataset.
2. Create a reproducible training/testing split.
3. Preprocess all images consistently.
4. Train the Eigenfaces representation using PCA.
5. Train the Fisherfaces representation using PCA followed by LDA where
   required.
6. Project training and test images into each learned feature space.
7. Compare a test feature vector with the training feature vectors.
8. Assign the identity of the closest suitable training representation.
9. Record correct and incorrect predictions.
10. Calculate and compare the recognition precision of both methods.
The distance metric, number of retained components, split strategy, and any
recognition threshold should be kept consistent or explicitly reported when
comparing the two algorithms.
## Evaluation
Recognition precision can be calculated as:
```text
precision = correct predictions / total test predictions
```
For a more useful evaluation, report at least:
- Number of identities
- Number of training images per identity
- Number of test images per identity
- Image dimensions
- Number of PCA components
- Number of LDA components, when applicable
- Distance metric
- Recognition threshold, if used
- Correct predictions
- Incorrect predictions
- Overall precision
If the dataset is small, a single train/test split may not be sufficient.
Repeated splits or cross-validation can give a more stable estimate, provided
that images from the same capture condition are not accidentally duplicated
across training and testing.
## Recognition threshold
A nearest feature vector is not automatically a valid recognition. A
recognition threshold can be used to reject a test image when its distance from
all known identities is too large. The threshold should be selected using
training/validation data and documented with the reported results.
A threshold that is too low may reject genuine identities. A threshold that is
too high may accept an unknown person as a known identity. Threshold values
should not be tuned using the final test results.
## Expected behavior
The observed precision depends on:
- Number and quality of images per identity
- Image alignment and crop quality
- Lighting and background consistency
- Facial expression and pose
- Image resolution
- Training/testing split
- Number of retained components
- Distance metric and recognition threshold
- Correctness of identity labels
Neither Eigenfaces nor Fisherfaces should be treated as a modern, general
purpose face-recognition system. They are classical educational and
experimental methods for understanding PCA, LDA, dimensionality reduction,
and identity classification.
## Reproducibility guidelines
For every experiment, record the following information:
- Dataset name and source
- Dataset directory structure
- Image dimensions
- Grayscale conversion method
- Training/testing split
- Random seed, if a random split is used
- PCA and LDA component counts
- Feature comparison or distance method
- Recognition threshold
- Precision and other evaluation results
Using the same dataset preparation and parameters for both methods is
essential. Changing the dataset split or preprocessing between experiments
can make the reported comparison misleading.
## Limitations
This approach is intended for controlled grayscale datasets. It does not
replace a modern face-recognition pipeline and should not be assumed to work
reliably with uncontrolled photographs, large pose changes, severe occlusion,
complex backgrounds, or arbitrary camera conditions.
The project also does not claim that a color image automatically improves this
specific experiment. Eigenfaces and Fisherfaces here operate on a carefully
prepared grayscale representation, where the effects of PCA and LDA can be
studied directly.
## Important note for anyone testing with another dataset
If another person wants to test this system with a different dataset, the
dataset **must be properly created and organized specifically for Eigenfaces
and Fisherfaces**. It must contain correctly labeled face classes, consistent
image dimensions, and suitable black-and-white/grayscale images.
This system is made **only for black-and-white images**. The project does not
force a color dataset to be converted into grayscale because a color dataset
is generally prepared for conventional face recognition, while this project is
specifically about face recognition using **Eigenfaces and Fisherfaces**.
Converting an unsuitable color dataset does not make its labels, image
conditions, or capture setup appropriate for this experiment.
There is also a minimum data requirement: use **at least 5 to 6 black-and-white
images per identity** so that the system has enough examples to learn and
evaluate each person. More images are recommended, especially when the images
contain different expressions, poses, or lighting conditions. A dataset with
too few images, inconsistent labels, or unsuitable color photographs may
produce unstable or misleading recognition results.
## Summary
This project provides a classical comparison between:
- PCA-based **Eigenfaces**, which preserve the most significant image
  variation; and
- LDA-based **Fisherfaces**, which use identity labels to improve separation
  between people.
The quality of the result depends as much on careful dataset construction and
consistent preprocessing as it does on the algorithm. For a meaningful
experiment, use a properly labeled grayscale dataset, keep the evaluation
conditions identical, and report the complete configuration together with the
recognition precision.

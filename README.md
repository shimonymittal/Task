# Task: Evaluation of Document Image Feature Extractor
## Key Points
1.	Feature Extractor: Each document image is turned into numerical vectors.
2.	Data:
    •	There are tens of thousands of unlabeled document images
    •	For every document type, we have exactly one labeled reference image.
3.	Goal: Evaluate how good the feature extractor classifies documents (by measure of accuracy, speed and robustness for example), and come up with a way to monitor its performance in production.

## Assumptions:
1.	The labelled reference vectors, obtained from feature extractor representing the documents, are high quality and define the document type appropriately in feature space.
2.	The vectors consider the semantic similarity between documents.
3.	Similar document types should have similar vectors and are therefore closer in the 256-dimensional space than dissimilar ones.
4.	The feature extractor is already trained; we’re only evaluating it.
5.	The document types are distinctly apart in feature space. 

## Approach:

### Method 1: Advance
### Method 2: Medium
### Method 3: Basic
#### Baseline classification system
This approach uses the Machine learning nearest-neighbour classification with unsupervised evaluation techniques:
It would be implementing KNN (k=1) classification using the labelled reference vectors:
- For each unlabelled document, compute the cosine similarity between its feature vector and all reference vectors.
- Assign the document the label of the most similar reference vector.

#### Evaluation Metrics:



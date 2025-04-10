# Task
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
5.	The document types are distinctly apart in feature space.Similarity 

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
##### Approach:
This approach uses the Machine learning nearest-neighbour classification with unsupervised evaluation techniques:
It would be implementing KNN (k=1) classification using the labelled reference vectors:
- For each unlabelled document, compute the cosine similarity between its feature vector and all reference vectors.
- Assign the document the label of the most similar reference vector.

##### Evaluation Metrics:
1. Clustering Quality Metrics
Since we lack ground truth labels for most documents, unsupervised metrics that can be used are:
- **Silhouette Score**: Measures how well-separated the clusters are defining the cluser quality.
- **Intra-class vs. Inter-class Distance Ratio**: Compare distances within preassumed classes to distances between classes.

2. Confidence Distribution Analysis
- Plot the distribution of similarity scores between documents and the assigned reference.
- Identify potential misclassifications through low similarity scores.
- Need to establish confidence thresholds for production use.

3. Manual Validation Sample
- Randomly sample a small set (e.g., 100 documents) for manual labelling.
- Use this to estimate classification accuracy and identify errors.
- Calculate precision, recall, and F1-score for this sample.

4. Production Monitoring Strategy
- **Confidence Thresholding**: Keep low-confidence classifications for human review.
- **Feedback Loop**: Incorporate verified classifications to improve the reference set.
- Production Monitoring: Track similarity scores over time. A drop might indicate feature extractor issues.

##### Limitations and Trade-offs:
1.	Reference Limitation: With only one reference per type, we can't capture intra-class variation.
2.	Scalability: Computing pairwise similarities can be expensive with very large datasets, leading to scalability issues.
3.	New Document: The system cannot identify document types without a reference image.

##### Future improvement:
1.	Active Learning: Implementing a feedback loop to identify uncertain classifications for human review.
2.	Synthetic Data Augmentation: Generating variations of reference images to better capture intra-class variation.
3.	Ensemble Approach: Combining multiple similarity metrics for more robust classification.
4.	Dimensionality Reduction: Applying techniques like PCA or t-SNE for visualization.
5.	Anomaly Detection: Implementing methods to identify documents that are either outkliers or don't match any known reference.




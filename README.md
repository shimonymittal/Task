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
#### Combination of Few-Shot Learning with CLIP (Vision Transformer) + Test-Time Adaptation (TTA) + Vector Database (FAISS/Qdrant)

##### Approach:
**Few-Shot Learning with CLIP (Vision Transformer)**

**Chosen**: It classifies documents using just one reference image per category as it is pretrained on diverse image data, so it generalizes well with less labels.

**Test-Time Adaptation (TTA) for Drift Detection**

**Chosen**:Makes sure to fix the drop in performance of the feature extractor before going to production.

**Vector Database (FAISS/Qdrant) for Scalable Search**

**Chosen**: Quickly find the closest reference vector among millions of documents.

1. Use the pretrained ViT to encode the labelled reference images to vectors.
2. Store these vectors as "prototypes" (one per document type) in FAISS/Qdrant.
3. Now take the unlabeled image, compute the CLIP vector
4. Compare it to all prototypes using FAISS search.
5. Assign the label of the closest prototype.
6. Track feature drift with TTA, it periodically compares new vectors to the baseline using KL divergence or Mahalanobis distance.
7. If drift is detected, fine-tune the feature extractor on new unlabeled data using Tent. It can update the model layers during inference. 

##### Evaluation Metrics:

1. Cosine Similarity Score
2. Top-1 Accuracy
3. FAISS/Qdrant benchmark tools
4. Feature Drift Score 

##### Limitations and Trade-offs:
1. Few-Shot  model performance can degrad for highly similar document types.
2. Single reference may not capture intra-class variations.
3. TTA  needs careful threshold tuning for drift detection.
4. In Vector DB, FAISS requires periodic re-indexing for new references.

##### Future improvement:
1. **Multimodal RAG**: We can think of incorporating OCR text with image features.
2. **Online Learning**: We can gradually update reference set with verified predictions.

### Method 2: Medium
#### Semi-Supervised Evaluation
##### Approach:
Semi-supervised learning framework leverages both the labelled references and the structure of the unlabelled data.
1. Manifold Learning and Embedding Visualization
First, I'll analyze the feature space to understand how well the extractor separates different document types:
- Apply dimensionality reduction (PCA or t-SNE) to visualize the 256-dimensional vectors in 2D/3D.
- Plot reference documents as reference points in this space.
- Examine if documents naturally form distinct clusters around their references.
- Quantify the quality of separation using neighborhood statistics.

2. Pseudo-Labelling with Confidence Thresholds
- For each unlabelled document, calculate similarity to all reference documents.
- Assign a pseudo-label only if the highest similarity exceeds a confidence threshold
- Leave ambiguous documents (below threshold) as "uncertain".
- Continuously iterate the threshold to optimum the precision-coverage tradeoff.

##### Evaluation Metrics:
Since we lack ground truth for most documents, we can consider:
1. Consistency-based metrics:
- Apply small perturbations to feature vectors and check for label consistency.
- For each document, check if its nearest neighbors got same label.
- If multiple images of the same document are existing, all should be classified with same label.

2. Active Learning Simulation
To estimate classification accuracy without extensive manual labelling:
- Identify the most informative documents for manual review using sampling maybe.
- Select documents at decision boundaries between clusters.
- Use this small labelled set to estimate overall accuracy.

3. Adversarial Evaluation
Test the robustness of the feature extractor:
- Generate synthetic variations of reference documents (rotation, cropping, lighting changes)
- Check how consistently these augmented dacuments are classified.
- This could help identify potential weaknesses in the feature extraction process.


##### Limitations and Trade-offs:
2.	**Threshold selection challenge**: Setting optimal confidence thresholds can be difficult as lot of tuning required.
3.	**Computational complexity**: Dimensionality reduction and neighbourhood analysis can be computationally expensive.
4.	**New document problem**: New document types with no reference will not be properly evaluated.
   
##### Future improvement:
1.	**Incremental learning**: Continuously update reference set with high-confidence examples
2.	**Hierarchical classification**: Group similar document types and create a two-stage classification process.
3.	**Ensemble of distance metrics**: Combine multiple similarity measures for more robust evaluation.
4.	**Contrastive learning**: Fine-tune the feature extractor using pairs of similar/dissimilar documents.
5.	**Explainability tools**: Develop methods to understand which regions of documents contribute most to classification.
6.	**Anomaly detection**: Implement dedicated methods to identify potentially fraudulent documents


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




### Association rules

- #### Causal relationship
	- 
		![[Pasted image 20261023110022.png]]
	
	- Example: Market basket analysis
	- buy (x, p1) ===> buy (x,p2), where p1 is a smartphone and p2 a cover.
	
	**Metrics**
	- Support: probability of A and B, P(A,B) > THs (support treshold)
	- Confidence: condicional probability between the two events (if one implies another). Refers to the causality
		- P (B | A) = P(A, B) / P(A) >= THc
	- These two metrics are descriptive and unsupervised

- #### Classification
	- Associate events to a class
		- ![[Pasted image 20261023112556.png|579]]
		
	- **Metrics**
		- True:
			- TP (true positive): elements that belongs to the class and have been correctly classified as part of the class.
			- TN (true negative): elements that don't belong to the class and have been correctly classified.
		- False:
			- FP (false positive): elements that don't belong to the class but have been uncorrectly classified.
			- FN (false negative): elements that belong to the class but have been uncorrecly classified.
			
		- Precision: TP / (TP + FP)
		- Recall: FP / (TP + FN)
		
	- **Example: decision tree**
	- These metrics are predictive and supervised.

- ### Clustering:
	- Generate groups based on similarity.
		- ![[Pasted image 20261023115032.png]]
		
	- Maximum similarity inside a group.
	- Minimum similarity between groups.
	- Requires a similarity function.
	
	- **K-MEANS**
		- K: number of groups.
		- Iterative approach
			1. Random centroid positioning.
			2. Assign elements to the nearest centroid.
			3. Reposition the centroid at the center of the group.
			4. Reassign the elements to the nearest centroid (step 2 again until we reach a point where the elements are not reassigned anymore).
		
	- These metrics are descriptive and unsupervised. 
	
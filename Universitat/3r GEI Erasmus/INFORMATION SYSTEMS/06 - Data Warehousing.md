**Different type of data than operational**

### New data model

Multidimensional model:
- Type of structure: Hypercube (cube with n dimensions)
	- Dimensions: Features of the data to analyse (coordinates)
	- Fact: elements of the analysis
	- Measures: quantitative information associated to a fact

Dimensions are organised hyerarchically.
- Example:
![[Pasted image 20261016105547.png|460]]

Each layer is an aggregation of the previous and we need to define an agregation function for each mesure.

![[Pasted image 20261016113149.png|534]]


**Data Warehouse:** DB of synthetic data, integrated and structured according to the analysis to support. Composed of several hypercubes.

**Data Warehousing:** DB + operations for populating , manage and query a Data Warehouse.

#### OLAP systems:
Properties:
- Fast
- Analytical
- Shared
- Multidimensional
- Informational

### DW architecture

![[Pasted image 20261016111522.png]]

#### Data mart:
- View of a Data Warehouse (DW) (a subset of the data)
- Possible applications:
	1. Dimensions.
	2. Granularity.
	3. Time.

#### ETL (Extraction, Transformation and Loading):

- Extraction: select which data to extract and also how (aggregation level that is needed).
	- Static extraction.
	- Implemental extraction.
- Transformation: different sources --> different formats.
	- Steps to follow to solve the problem:
		1. Cleaning: remove errors (missing data, inconsistencies, out of range data).
		2. Reconciliation: find relations between the data referring to the same object.
		3. Standarisation: apply the same format to all the data even if they are comming from different sources.
- Loading: load the data according to the hypercube model.

### Conceptual model:

Dimensional Fact Model (DFM): 

![[Pasted image 20261016113908.png|674]]

### Logical models for DW

1. Multidimensional OLAP (MOLAP):
	- Multidimensional DB and multidimensional engine
		-  + Coherent with the conceptual model
		-  + Efficient queries
		-  - No standard solution
		-  - Data volume (inneficient memory occupation)
			- Allocate memory for each combination of dimensions

2. Relational OLAP (ROLAP):
	- Relational DB with a SQL engine
		-  + Based on standards
		-  + Well-known technology
		-  + Limited storage occupation
		-  - No coherent with the conceptual model
		- Inefficient query execution due to multiple joins 

3. Hybrid OLAP (HOLAP):
	- ROLAP for DW and MOLAP for data marts

#### OLAP operators

![[Pasted image 20261016151116.png]]

- Roll-up: increase aggregation on a dimension
	- Ex. Day ---> Month
- Drill-down: reduce aggregation on a dimension
- Slice: select one value for one dimension
- Dice: select a subset of calues for multiple dimentions

![[Pasted image 20261016152035.png|605]]

![[Pasted image 20261016152341.png|351]]![[Pasted image 20261016152354.png|335]]


### Data Mining

Automatically extract relevant information (patterns / trends) from a  (big) dataset.
1. Select data of interest.
2. Clean data.
3. Data integration.
4. Data transformation / resharping.
5. Analysis of the data (algorithm).
6. Results evaluation and reduction.
7. Presentation to the users.

The first 4 can be performed and executed by the ETL tool.

**Focus on point number 5:**

Captura pantalla 15:52

#### Classification of DM algorithms
1. 
	- Supervised:
		- Use labeled datasets.
	- Non-supervised:
		- Use raw data.

2. 
	- Descriptive
		- Analyse to generalize what is observed.
	- Predictive
		- Models to predict future events.

3 major algorithms:
- Association rules
- Classification
- Clustering



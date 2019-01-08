# Description

The function DreamAI imputes a dataset with missing values or NA's using 7 different methods: 

 - "KNN": k nearest neighbor 
 - "MissForest": nonparametric Missing Value
   Imputation using Random Forest 
  - "ADMIN": abundance dependent missing
   imputation
   - "Brinn": imputation using IRNN-SCAD algorithm 
   - "SpectroFM": imputation using matrix factorization 
   -  "RegImpute": imputation using Glmnet ridge regression  
   -  "Ensemble": aggregation of the 6 methods
   using simple average.

## Usage
```
DreamAI(data, k = 10, maxiter_MF = 10, ntree = 100,
  maxnodes = NULL, maxiter_ADMIN = 30, tol = 10^(-2),
  gamma_ADMIN = NA, gamma = 50, CV = FALSE,
  fillmethod = "row_mean", maxiter_RegImpute = 10,
  conv_nrmse = 1e-06, iter_SpectroFM = 40, method = c("KNN",
  "MissForest", "ADMIN", "Brinn", "SpectroFM", "RegImpute"),
  out = c("Ensemble"))
```
## Arguments
  
| Parameter                 | Default       | Description   |	
| :------------------------ |:-------------:| :-------------|
| data	       |	           |dataset in the form of a matrix or dataframe with missing values or NA's. The function throws an error message and stops if any row or column in the dataset is missing all values
| k         | 10           |number of neighbors to be used in the imputation by KNN and ADMIN
| maxiter_MF 	       |	10	            |maximum number of iteration to be performed in the imputation by "MissForest" if the stopping criteria is not met beforehand
| ntree  		       | 100	           | number of trees to grow in each forest in "MissForest"
| maxnodes		           | NULL             | maximum number of terminal nodes for trees in the forest in "MissForest", has to equal at least the number of columns in the given data
| maxiter_ADMIN 	        | 30           | maximum number of iteration to be performed in the imputation by "ADMIN" if the stopping criteria is not met beforehand
| tol	         | 10^(-2)             | convergence threshold for "ADMIN"
| gamma_ADMIN          | NA           | parameter for ADMIN to control abundance dependent missing. Default is 0 (for log ratio intensity data). For abundance data put gamma=NA, and it will be estimated accordingly
| gamma       | 50  | parameter of the supergradients of popular nonconvex surrogate functions, e.g. SCAD and MCP of L0-norm for Brinn
| CV   | FALSE         | a logical value indicating whether to fit the best gamma with cross validation for "Brinn". If CV=FALSE, default gamma=50 is used, while if CV=TRUE gamma is calculated using cross-validation.
| fillmethod			             | "row_mean" 	           | a string identifying the method to be used to initially filling the missing values using simple imputation for "RegImpute". That could be "row_mean" or "zeros", with "row_mean" being the default. It throws an warning if "row_median" is used.
| maxiter_RegImpute			     | 10         | maximum number of iterations to reach convergence in the imputation by "RegImpute"
| conv_nrmse			             | 1e-06     	     | convergence threshold for "RegImpute"
| iter_SpectroFM		    | 40     	     | number of iterations for "SpectroFM"
| method		      | c("KNN","MissForest", "ADMIN", "Brinn", "SpectroFM", "RegImpute", "Ensemble")     	   | a vector of imputation methods: ("KNN", "MissForest", "ADMIN", "Brinn", "SpectroFM, "RegImpute", "Ensemble"). Default is "Ensemble" if nothing is specified
| out		      | c("Ensemble")     	   | a vector of imputation methods for which the function will output the imputed matrices

	
# Value
a list of imputed datasets by different methods as specified by the user. Always returns imputed data by "Ensemble"

# Note
If all methods are specified for obtaining "Ensemble" imputed matrix, the approximate time required to output the imputed matrix for a dataset of dimension 26000 x 200 is ~50 hours.

# Examples
```
data(datapnnl)
data<-datapnnl.rm.ref[1:100,1:21]
impute<- DreamAI(data,k=10,maxiter_MF = 10, ntree = 100,maxnodes = NULL,maxiter_ADMIN=30,tol=10^(-2),gamma_ADMIN=NA,gamma=50,CV=FALSE,fillmethod="row_mean",maxiter_RegImpute=10,conv_nrmse = 1e-6,iter_SpectroFM=40, method = c("KNN", "MissForest", "ADMIN", "Brinn", "SpectroFM", "RegImpute"),out="Ensemble")
impute$Ensemble
```

<!-- ---
layout: default
---

{% include mathjax.html %} -->

# NetREm
## Network regression embeddings reveal cell-type transcription factor interactions for gene regulation
<!-- ##### GRegNet Gene Regular(ized/atory) Network -->

### By: Saniya Khullar, Xiang Huang, Raghu Ramesh, John Svaren, Daifeng Wang
[Daifeng Wang Lab](https://daifengwanglab.org/) <br>

## Case: Additional Gene Expression Nodes that are not found in the Protein-Protein Interaction Network

NetREm is a software package that utilizes network-constrained regularization for biological applications and other network-based learning tasks. In biology, traditional regression methods can struggle with correlated predictors, particularly transcription factors (TFs) that regulate target genes (TGs) in gene regulatory networks (GRNs). NetREm incorporates information from prior biological networks to improve predictions and identify complex relationships among predictors. This approach can highlight important nodes and edges in the network, reveal novel regularized embeddings for genes, provide insights into underlying biological processes, and improve model accuracy and biological/clinical significance of the models. NetREm can incorporate multiple types of network data, including Protein-Protein Interaction (PPI) networks, gene co-expression networks, and metabolic networks. 


## Pipeline

*Pipeline image of NetREm*
![png](netrem_pipeline.PNG)


## Hardware Requirements

The minimum requirement is a computer with 8 GB of RAM and 32 GB of storage. For large prior graph networks, 32 GB of RAM is recommended. 

## Software Requirements and Installation Guide
The software uses Python 3.10. After downloading the NetREm Github code, conda/Anaconda users can use the following steps to install:
1. In the Anaconda navigator prompt, create a virtual environment of Python 3.10 by running:<br>
`conda create -n NetREm python=3.10`
2. Activate the environment:<br>
`conda activate NetREm`
3. Make sure to change the current directory to the NetREm folder.<!-- Saniya: This is operating system dependent, you may add command to change directory for Windows/Madc/Linux, cd for windows/Linux, what Shanw used for Windows is cd C:\C:\Users\Shawn\Code\NetREm -->
4. Install the packages and dependencies (*math, matplotlib, networkx, numpy, typing, os, pandas, plotly.express, random, scipy, scikit-optimize, sklearn, sys, tqdm, warnings*):<br>
`pip install -r requirements.txt`

<!-- To install these packages manually, please run *pip install [package]* or *pip3 install [package]* in the terminal or run *conda install [package]* in the Anaconda navigator prompt. -->



## Usage of the NetREm main function netrem()
NetREm fits a Network-constrained Lasso regression machine learning model with user-provided weights for the prior network.  Here, **netrem** is the main function with the following usage:
<!-- For biological applications, it is recommended that the user ensure network names map to gene expression names -->
<!-- SHould we have 2 arguments? 1 for default_edge_weight for nodes found in network. default_weight_prior_edges: for any edge in the edge_list that has an unknown weight, we provide this edge_weight. Thus, we are prioritizing edges provided in the edgelist over those not found in the edge_list originally. Then we can show that since we skipped out on sharing an edge, the code automatically added in an edge with lower edge weight.  
default_weight_new_edges. -->
**netrem**(<br> 
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*edge_list*, <br>
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*beta_net = 1*, <br>
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*alpha_lasso = 0.01*, <br>
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*default_edge_weight = 0.1*,<br>
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*degree_threshold = 0.5*,<br>
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*gene_expression_nodes = []*,<br>
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*overlapped_nodes_only = False*,<br>
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*y_intercept = False*, <br>
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*view_network = False*, <br>
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*model_type = "Lasso"*,<br>
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;...<br>
)
<!-- degree_pseudocount = 1e-3,<br> -->

<!-- has 2 options with respect to the alpha_lasso_val ($\alpha_{lasso} \geq 0$) for the lasso regularization on the overall model: 
* default: the user may specify $\alpha_{lasso}$ manually (if *cv_for_alpha_lasso_model_bool = False*). If no alpha_lasso_val is specified, 0.1 will be used. 
* alternative: the user may opt for GRegulNet to select $\alpha_{lasso}$ based on cross-validation (CV) on training data (if *cv_for_alpha_lasso_model_bool = True*) -->

<!-- Ultimately, this function uses a prior network edge list and $\beta_{network}$ to build an estimator object from the class GRegulNet. This estimator can then take in input $X$ and $y$ data:  transforms them to $\tilde{X}$ and $\tilde{y}$, respectively, and use them to fit a Lasso regression model with a regularization value of $\alpha_{lasso}$. Overall, the trained model is more reflective of an underlying network structure among predictors and may be more biologically meaningful and interpretable.  -->




<!-- $$
\begin{cases}
  \text{geneRegulatNet(edge_list, } \beta_{network}, \text{cv_for_alpha_lasso_model_bool = } False, \alpha_{lasso}\text{)} & \text{if cv_for_alpha_lasso_model_bool = } False \\
  \text{geneRegulatNet(edge_list, } \beta_{network}, \text{cv_for_alpha_lasso_model_bool = } True) & \text{if cv_for_alpha_lasso_model_bool = } True \\
\end{cases}
$$

There are several additional parameters that can be adjusted in the geneRegulatNet function, which will be explained later in the *Default Parameters* section.  -->

<!-- ### Main Input: -->

<!-- * *edge_list*: A list of lists corresponding to a prior network involving the predictors (as nodes) and relationships among them as edges. We will utilize this prior network to constrain our machine learning model. For instance, this could be a Protein-Protein Interaction (PPI) network of interactions among the predictors. If  weights are missing for any edge, then the default_weight will be used for that respective edge. We assume that this network is undirected and thereby symmetric, so the user only needs to specify edges in 1 direction (and the other direction will be assumed automatically). 

For instance:

[[source<sub>1</sub>, target<sub>1</sub>, weight<sub>1</sub>], [source<sub>2</sub>, target<sub>2</sub>, weight<sub>2</sub>], ..., [source<sub>Z</sub>, target<sub>Z</sub>, weight<sub>Z</sub>]]. 

Where weight<sub>1</sub>, weight<sub>2</sub>, ..., weight<sub>Z</sub> are optional. If an edge is missing its respective edge weight, then the default edge weights will be utilized. 

The edge_list will be represented by:

| Source | Target |  Weight |
| --------- | ---------- | ---------- |
|source<sub>1</sub>   | target<sub>1</sub> | weight<sub>1</sub>|
|source<sub>2</sub>   | target<sub>2</sub> | weight<sub>2</sub> |
|...    | ... | ... |
|source<sub>Z</sub>    | target<sub>Z</sub> | weight<sub>Z</sub>|
|target<sub>1</sub>   | source<sub>1</sub> | weight<sub>1</sub> |
|target<sub>2</sub>    | source<sub>2</sub> | weight<sub>2</sub> |
|...    | ... | ... |
|target<sub>Z</sub>    | source<sub>Z</sub> | weight<sub>Z</sub> | -->

<!-- * *beta_network_val*:  A numerical value for $\beta_{network} \geq 0$.  -->

<!-- * *cv_for_alpha_lasso_model_bool*:
  - False (default): user wants to specify the value of $\alpha_{lasso}$
  - True: GRegulNet will perform cross-validation (CV) on training data to determine optimal $\alpha_{lasso}$ -->

<!-- $$ = \begin{cases}
  \text{if cv_for_alpha_lasso_model_bool = } False & \text{default: user wants to specify the value of }  \alpha_{lasso}  \\
  \text{if cv_for_alpha_lasso_model_bool = } True & \text{GRegulNet will perform cross-validation (CV) on training data to determine optimal } \alpha_{lasso} \\
\end{cases}
$$ -->

<!-- $$ = \begin{cases}
  \text{if cv_for_alpha_lasso_model_bool = } False & \text{default: user wants to specify the value of }  \alpha_{lasso}  \\
  \text{if cv_for_alpha_lasso_model_bool = } True & \text{GRegulNet will perform cross-validation (CV) on training data to determine optimal } \alpha_{lasso} \\
\end{cases}
$$ -->


<!-- ##### If *cv_for_alpha_lasso_model_bool* is False, we need to specify alpha_lasso_val $\alpha_{lasso}$ ##### -->

<!-- * *alpha_lasso_val*:  A numerical value for $\alpha_{lasso} \geq 0$. If *cv_for_alpha_lasso_model_bool* is False, the user is then advised to specify this $\alpha_{lasso}$ parameter (alpha_lasso_val). Otherwise, if no $\alpha_{lasso}$ value is specified, then the default value of $\alpha_{lasso} = 0.1$ will be used.  -->

<!-- | Parameter | Definition | Default |
| --------- | ---------- | ---------- |
| edge_list       | list of lists: [[source<sub>1</sub>, target<sub>1</sub>, weight<sub>1</sub>], ..., [source<sub>Z</sub>, target<sub>Z</sub>, weight<sub>Z</sub>]] | value needed |
| $\beta_{network}$  | Regularization parameter for network penalization | value needed |
| cv_for_alpha_lasso_model_bool  | Should GRegulNet perform Cross Validation to determine $\alpha_{lasso}$  | False |
| $\alpha_{lasso}$  | Regularization parameter for lasso | value needed if cv_for_alpha_lasso_model_bool = False; default: 0.1 | -->


| Parameter | Definition | 
| --------- | ---------- | 
| **edge_list**      | ***list*** <br> A list of lists corresponding to a prior network involving predictors (nodes) and relationships among them (edges): <br> [[source<sub>1</sub>, target<sub>1</sub>, weight<sub>1</sub>], ..., [source<sub>Z</sub>, target<sub>Z</sub>, weight<sub>Z</sub>]]. Here, weight<sub>1</sub>, ..., weight<sub>Z</sub> are optional. Nodes found in the `edge_list` are referred to as *network nodes* | 
| **beta_net** | ***float, default = 1*** <br> Regularization parameter for network penalization: $\beta_{net} \geq 0$. | 
| **alpha_lasso**  | ***float, default = 0.01*** <br> A numerical regularization parameter for the lasso term ($\alpha_{lasso} \geq 0$) needed if `model_type = LassoCV`. Larger values typically reduce the number of final predictors in the model. |
| **default_edge_weight**  | ***float, default = 0.1*** <br>  Default edge weight ($w$) assigned to any edge with missing weight | 
| **degree_threshold**  |  ***float, default = 0.5*** <br>  Edges with weight $w$ > degree_threshold are counted as 1 towards the node degree $d$ |
| **gene_expression_nodes**      | ***list, default = []*** <br> A list of predictors (e.g. TFs) to use that typically is found as columns in the training gene expression data $X_{train}$. <br> Any `gene_expression_nodes` not found in the `edge_list` are added internally into the network prior `edge_list` using pairwise `default_edge_weight`. Specifying `gene_expression_nodes` is *optional* but may boost the speed of training and fitting NetREm models (by adjusting the network prior in the beginning). Thus, if the gene expression data ($X$) is available, it is recommended to input `gene_expression_nodes`. Otherwise, NetREm  automatically determines `gene_expression_nodes` when fitting the model with $X_{train}$ gene expression data (when *fit(X,y)* is called), but needs time to recalibrate the network prior based on $X_{train}$ nodes and value set for `overlapped_nodes_only`. |
| **overlapped_nodes_only**      | ***boolean, default = False*** <br> This determines if NetREm should focus on common nodes found in *network nodes* (from `edge_list`) and gene expression data (based on `gene_expression_nodes`). Here, *network nodes* not found in the gene expression data will always be removed. The priority is given to `gene_expression_nodes` since those have gene expression values that are used by the regression. <br> • If `overlapped_nodes_only = False`, the predictors will come from `gene_expression_nodes`, even if those are not found in the network `edge_list`. Some predictors may lack relationships in the prior network. <br> • If `overlapped_nodes_only = True`, the predictors used will need to be a common node: *network node* also found in the `gene_expression_nodes`. <br> | 
| **y_intercept** | ***boolean, default = 'False'*** <br> This is the `fit_intercept` parameter found in the [Lasso](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.Lasso.html) and [LassoCV](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LassoCV.html) classes in sklearn. <br> • If `y_intercept = True`, the model will be fit with a y-intercept term included. <br> • If `y_intercept = False`, the model will be fit with no y-intercept term. | 
| **view_network**  |  ***boolean, default = False*** <br>  • If `view_network = True`, then NetREm outputs visualizations of the prior graph network. Recommended for small networks (instead of for dense hairballs) <br> If `view_network = False`, then NetREm saves time by not outputting visuals of the network.  |
| **model_type** | ***{'Lasso', 'LassoCV'}, default = 'Lasso'*** <br> • Lasso: user specifies value of $\alpha_{lasso}$ <br> • LassoCV: NetREm performs cross-validation (CV) on training data to determine optimal $\alpha_{lasso}$  | 
| **... (additional parameters)** |Read more in the [User Guide: Additional Parameters](https://github.com/SaniyaKhullar/NetREm/blob/main/user_guide/Additional_NetREm_Parameters.md) for more parameters after **model_type** |


### Details:

 We input an edge list of the prior graph network (constrains the model via network-based regularization) and a beta_net ($\beta_{net} \geq 0$, which scales the network-based regularization penalty). The user may specify the alpha_lasso ($\alpha_{lasso} \geq 0$) manually for the lasso regularization on the overall model (if *model_type = Lasso*) or NetREm may select an optimal $\alpha_{lasso}$ based on cross-validation (CV) on the training data (if `model_type = LasssoCV`). Then, **netrem** builds an estimator object from the class Netrem that can then take in input $X$ and $y$ data: transforms them to $\tilde{X}$ and $\tilde{y}$, respectively, and use them to fit a Lasso regression model with a regularization value of $\alpha_{lasso}$. Ultimately, the trained NetREm machine learning model is more reflective of an underlying network structure among predictors and may be more biologically meaningful and interpretable. Nonetheless, NetREm could be applied in various contexts where a network structure is present among the predictors. 

### Output Values: ###

* A NetREm network-regularized linear model estimator object from the NetREmModel class. There are several methods we can call for our NetREm estimator object:

#### Methods:

We assume that our $X$ and $y$ data correspond to $M$ samples and $N$ predictors. For biological applications, the $X$ data would typically be gene expression data (bulk or single-cell) for the $N$ predictors (usually Transcription Factors (TFs)) for the $M$ samples. Then, the $y$ values would correspond to the gene expression values for the target gene (TG) $y$ for those same $M$ samples. 

* **fit($X$, $y$)**

Building and training the NetREm model with $X$ and $y$ data. 

| Parameter | Definition | 
| --------- | ---------- | 
| $X$ | [Pandas](https://pandas.pydata.org/) dataframe ($M$ rows by $N$ columns) where the rows are samples and columns are predictors.  | 
| $y$ | [Pandas](https://pandas.pydata.org/) dataframe ($M$ rows by 1 column) with 1 column that corresponds to values for the response variable for the same samples found in $X$. | 



We can retrieve our model coefficients and other attributes by calling these outputs:

| Output | Definition | 
| --------- | ---------- | 
| model_coef_df  | [Pandas](https://pandas.pydata.org/) dataframe of the Lasso model coefficients for all of the predictors and y-intercept (if `y_intercept = True`) | 
| model_nonzero_coef_df  | Filtered [Pandas](https://pandas.pydata.org/) dataframe of the Lasso model coefficients for only the predictors and y-intercept (if `y_intercept = True`) that have non-zero values. | 
| optimal_alpha  | If `model_type = LassoCV`, returns the optimal $\alpha_{lasso}$ found by performing cross validation (CV) on training data | 
| predY_train | NetREm's predicted values for the training response $y$ data (used to fit the model). | 
| mse_train | Mean Square Error (MSE): predicted `predY_train` versus actual training values for the response variable Y. | 
| sorted_coef_df | [Pandas](https://pandas.pydata.org/) dataframe that sorts the final model coefficients (including the y-intercept) based on their absolute values. The rank is provided from least (most important: highest absolute value coefficient) to highest (least important in model). | 
| final_corr_vs_coef_df | [Pandas](https://pandas.pydata.org/) dataframe with 3 rows. <br> • NetREm regression coefficient for predictor <br> • correlation of each predictor with y based on the training data <br> • absolute value ranking of the coefficients for the predictors |
| combined_df | [Pandas](https://pandas.pydata.org/) dataframe with a row for each predictor and several columns detailing:<br> • general NetREm model information: `y_intercept`, train MSE, `beta_net`, `alpha_lasso`, original number of predictors in $X$, filtered number of predictors input to NetREm (based on pre-processing by user), final number of non-zero predictors selected <br>
• predictor-specific results: NetREm coefficient for predictor, absolute value of NetREm coefficient, rank of the absolute value of the coefficient (low ranks imply higher | NetREm coefficient | ) |


<!-- | all_params_list  | List of lists of the parameters used for NetREm model (defensive programming) | 
| network_params | List of lists of the parameters for the prior network used for NetREm model (defensive programming). <br> [] |  -->


* **predict($X$)** 

We can use our model to predict values for our response variable $y$. 

| Parameter | Definition | 
| --------- | ---------- | 
| $X$ | [Pandas](https://pandas.pydata.org/) dataframe ($M$ rows by $N$ columns) where the rows are samples and columns are predictors.  | 

<!-- | $X$ | Input numpy array matrix (list of lists) where each list corresponds to a sample. Here, rows are samples and columns are predictors. |  -->

*Returns:*
  Numpy array of $\hat{y}$ predicted values for $y$.

* **test_mse($X$, $y$)**

We can evaluate our model performance capabilities on data like testing data using the Mean Squared Error (MSE) as our metric. 

| Parameter | Definition | 
| --------- | ---------- | 
| $X$ | [Pandas](https://pandas.pydata.org/) dataframe ($M$ rows by $N$ columns) where the rows are samples and columns are predictors.  | 
| $y$ | [Pandas](https://pandas.pydata.org/) dataframe ($M$ rows by 1 column) with 1 column that corresponds to values for the response variable for the same samples found in $X$. | 


<!-- | Parameter | Definition | 
| --------- | ---------- | 
| $X$ | Numpy array matrix (list of lists) where each list corresponds to a sample. Here, rows are samples and columns are predictors. | 
| $y$ | Numpy array list for response variable with 1 value for each sample.|  -->

*Returns:*
    Numeric value corresponding to the Mean Square Error (MSE). 
  
$$MSE = \frac{1}{m} \sum_{i=1}^m (y_i - \hat{y_i})^2$$

## Demo (Toy Example) of NetREm:
The goal is to build a machine learning model to predict the gene expression levels of our target gene (TG) $y$ based on the gene expression levels of $N = 6$ Transcription Factors (TFs) [TF<sub>1</sub>, $TF_{2}$, $TF_{3}$, $TF_{4}$, $TF_{5}$, $TF_{6}$] in a particular cell-type. Assume the gene expression values for each TF are [X<sub>1</sub>, $X_{2}$, $X_{3}$, $X_{4}$, $X_{5}$, $X_{6}$], respectively. We generate $M = 100$ random samples (rows) of data where the Pearson correlations ($r$) between gene expression of each TF ($X$) with gene expression of TG $y$ as *corrVals*: [cor(TF<sub>1</sub>, $y$) = 0.9, cor(TF<sub>2</sub>, $y$) = 0.5, cor(TF<sub>3</sub>, $y$) = 0.1, cor(TF<sub>4</sub>, $y$) = -0.2, cor(TF<sub>5</sub>, $y$) = -0.8,  cor(TF<sub>6</sub>, $y$) = -0.3]. 

The dimensions of $X$ are therefore 100 rows by 6 columns (predictors). More details about our *generate_dummy_data* function (and additional parameters we can adjust for) are in [Dummy_Data_Demo_Example.ipynb](https://github.com/SaniyaKhullar/NetREm/blob/main/Dummy_Data_Demo_Example.ipynb). Our NetREm estimator also incorporates a constraint of an **undirected weighted prior graph network** of biological relationships among only 5 TFs based on a weighted Protein-Protein Interaction (PPI) network ([TF<sub>1</sub>, $TF_{2}$, $TF_{3}$, $TF_{4}$, $TF_{5}$]), where higher edge weights $w$ indicate stronger biological interactions at the protein-level.

The code for this demo example is [demo_toy.py](https://github.com/SaniyaKhullar/NetREm/blob/main/demo/demo_toy.py) in the *demo* folder.

```python 
from DemoDataBuilderXandY import generate_dummy_data
from Netrem_model_builder import netrem
import PriorGraphNetwork as graph
import error_metrics as em 
import essential_functions as ef
import netrem_evaluation_functions as nm_eval

dummy_data = generate_dummy_data(corrVals = [0.9, 0.5, 0.1, -0.2, -0.8, -0.3],
                                 num_samples_M = 100,
                                 train_data_percent = 70)
```
The Python console or Jupyter notebook will  print out the following:

    same_train_test_data = False
    Please note that since we hold out 30.0% of our 100 samples for testing, we have:
    X_train = 70 rows (samples) and 6 columns (N = 6 predictors) for training.
    X_test = 30 rows (samples) and 6 columns (N = 6 predictors) for testing.
    y_train = 70 corresponding rows (samples) for training.
    y_test = 30 corresponding rows (samples) for testing.

The $X$ data should be in the form of a Pandas dataframe as below:

```python
X_df = dummy_data.X_df
X_df.head()
```
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TF1</th>
      <th>TF2</th>
      <th>TF3</th>
      <th>TF4</th>
      <th>TF5</th>
      <th>TF6</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2.020840</td>
      <td>0.594445</td>
      <td>-1.443012</td>
      <td>-0.688777</td>
      <td>0.900770</td>
      <td>-2.643671</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3.224776</td>
      <td>-0.270632</td>
      <td>-0.557771</td>
      <td>-0.305574</td>
      <td>0.054708</td>
      <td>-1.054197</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-2.746721</td>
      <td>1.502236</td>
      <td>2.043813</td>
      <td>1.252975</td>
      <td>2.082159</td>
      <td>1.227615</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-0.558130</td>
      <td>1.290771</td>
      <td>-1.230527</td>
      <td>-0.678410</td>
      <td>0.630084</td>
      <td>-1.508758</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-2.181462</td>
      <td>-0.657229</td>
      <td>-2.880186</td>
      <td>-1.629470</td>
      <td>0.268042</td>
      <td>1.207254</td>
    </tr>
  </tbody>
</table>
</div>




```python
y_df = dummy_data.y_df
y_df.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>y</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.601721</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.151619</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-1.359462</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.222055</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-0.775868</td>
    </tr>
  </tbody>
</table>
</div>

```python
# 70 samples for training data (used to train and fit GRegulNet model)
X_train = dummy_data.view_X_train_df()
y_train = dummy_data.view_y_train_df()

# 30 samples for testing data
X_test = dummy_data.view_X_test_df()
y_test = dummy_data.view_y_test_df()

# prior network edge_list:
edge_list = [["TF1", "TF2", 0.9], ["TF4", "TF5", 0.75], ["TF1", "TF3"], ["TF1", "TF4"], ["TF1", "TF5"], 
             ["TF2", "TF3"], ["TF2", "TF4"], ["TF2", "TF5"], ["TF3", "TF4"], ["TF3", "TF5"]]

beta_network_val = 3 
# by default, cv_for_alpha is False, so alpha_lasso_val will be specified for the alpha_lasso parameter.
alpha_lasso_val = 0.01

# Building the network regularized regression model: 
# Please note: To include nodes found in the gene expression data that are not found in the PPI Network (e.g. TF6 in our case), we use False for the overlapped_nodes_only argument (otherwise, we would only use TFs 1 to 5):
netrem_demo = netrem(edge_list = edge_list, 
                     beta_net = beta_network_val,
                     alpha_lasso = alpha_lasso_val,
                     view_network = True)

# Fitting the NetREm model on training data: X_train and y_train:
netrem_demo.fit(X_train, y_train)
```

    
![png](output_3_1.png)
    



    
![png](output_3_2.png)
    


   1 new node(s) added to network based on gene expression data ['TF6']
    

    
![png](output_3_5.png)

![png](netrem_estimator.PNG)

<!-- There is a particularly strong relationship between $TF_{1} \leftrightarrow TF_{2}$ of 0.9 and between $TF_{4} \leftrightarrow TF_{5}$ of 0.75. The remaining relationships among the other TFs is assumed to be the default (edge weight of 0.1). -->
<!-- Here, $gregulnet_{demo}$ is an object of the *GRegulNet* class. We fit a model using $X_{train}$ and $y_{train}$ data (70 samples). -->

To view and extract the predicted model coefficients for the predictors: 

<!-- ```python
gregulnet_demo.coef
```

    array([ 0.23655573,  0.11430656,  0.00148755, -0.03512912, -0.16009479]) -->


```python
netrem_demo.model_coef_df
```

<!-- <div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>y_intercept</th>
      <th>TF1</th>
      <th>TF2</th>
      <th>TF3</th>
      <th>TF4</th>
      <th>TF5</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>None</td>
      <td>0.236556</td>
      <td>0.114307</td>
      <td>0.001488</td>
      <td>-0.035129</td>
      <td>-0.160095</td>
    </tr>
  </tbody>
</table>
</div> -->

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>y_intercept</th>
      <th>TF1</th>
      <th>TF2</th>
      <th>TF3</th>
      <th>TF4</th>
      <th>TF5</th>
      <th>TF6</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>None</td>
      <td>0.309776</td>
      <td>0.112297</td>
      <td>0.001116</td>
      <td>-0.073603</td>
      <td>-0.21665</td>
      <td>0.000375</td>
    </tr>
  </tbody>
</table>
</div>

In the context of gene regulation (in biology), we predict that predictors with negative NetREm coefficients for target gene (TG) $y$ may be repressors (their activity focuses on reducing expression of $y$) and those with positive coefficients for $y$ may be activators. 

To view the cell-type-specific Protein-Protein Interactions (PPIs) that NetREm learned for this target gene $y$, we can view the `B_interaction_df`.  

```python
netrem_demo.B_interaction_df
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TF1</th>
      <th>TF2</th>
      <th>TF3</th>
      <th>TF4</th>
      <th>TF5</th>
      <th>TF6</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>TF1</th>
      <td>4.512391</td>
      <td>0.856197</td>
      <td>-0.515540</td>
      <td>-0.262458</td>
      <td>-2.315411</td>
      <td>-1.166994</td>
    </tr>
    <tr>
      <th>TF2</th>
      <td>0.856197</td>
      <td>1.644936</td>
      <td>-0.342697</td>
      <td>0.169874</td>
      <td>-0.570895</td>
      <td>-0.494075</td>
    </tr>
    <tr>
      <th>TF3</th>
      <td>-0.515540</td>
      <td>-0.342697</td>
      <td>84.228863</td>
      <td>-0.470847</td>
      <td>-0.618672</td>
      <td>-16.642297</td>
    </tr>
    <tr>
      <th>TF4</th>
      <td>-0.262458</td>
      <td>0.169874</td>
      <td>-0.470847</td>
      <td>1.218198</td>
      <td>0.070268</td>
      <td>-0.619841</td>
    </tr>
    <tr>
      <th>TF5</th>
      <td>-2.315411</td>
      <td>-0.570895</td>
      <td>-0.618672</td>
      <td>0.070268</td>
      <td>2.505441</td>
      <td>-0.163531</td>
    </tr>
    <tr>
      <th>TF6</th>
      <td>-1.166994</td>
      <td>-0.494075</td>
      <td>-16.642297</td>
      <td>-0.619841</td>
      <td>-0.163531</td>
      <td>84.577403</td>
    </tr>
  </tbody>
</table>
</div>

We predict that positive scores between predictors (e.g. $TF_1$ and $TF_2$ have a score of 0.856197) tend to imply potential cooperativity (e.g. cobinding) among them. Negative scores may suggest antagonistic activity between the TFs (e.g. $TF_1$ and $TF_5$ have a score of -2.315411), where these TFs may compete to regulate the $TG$ through biological mechanisms that may be investigated further through experiments.


We can test the performance of our data on testing data $X_{test}$ ($M = 30$ samples), to understand better the generalizability of our NetREm model on new, unseen, data. 


```python
pred_y_test = netrem_demo.predict(X_test) # predicted values for y_test
mse_test = netrem_demo.test_mse(X_test, y_test)

print(f"The testing Mean Square Error (MSE) is {mse_test}")
```

    The testing Mean Square Error (MSE) is 0.10939471847175668


We can analyze more metrics about our NetREm model results as below: 

```python
netrem_demo.final_corr_vs_coef_df
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>info</th>
      <th>input_data</th>
      <th>TF1</th>
      <th>TF2</th>
      <th>TF3</th>
      <th>TF4</th>
      <th>TF5</th>
      <th>TF6</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>network regression coeff. with y: y</td>
      <td>X_train</td>
      <td>0.309776</td>
      <td>0.112297</td>
      <td>0.001116</td>
      <td>-0.073603</td>
      <td>-0.21665</td>
      <td>0.000375</td>
    </tr>
    <tr>
      <th>0</th>
      <td>corr (r) with y: y</td>
      <td>X_train</td>
      <td>0.90244</td>
      <td>0.440681</td>
      <td>0.058722</td>
      <td>-0.163072</td>
      <td>-0.814564</td>
      <td>-0.252551</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Absolute Value NetREm Coefficient Ranking</td>
      <td>X_train</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
      <td>4</td>
      <td>2</td>
      <td>6</td>
    </tr>
  </tbody>
</table>
</div>


In the context of gene regulation, our results may thereby be interpreted in the [following way](https://github.com/SaniyaKhullar/NetREm/blob/main/netrem_final_demo.png).

Nonetheless, NetREm can be applied to solve a suite of regression problems where there is an underlying connection among the predictors and their correlation with one another may be utilized jointly for the predictive task rather than discarded. 

We also provide a suite of evaluation functions and explanations of more advanced functionalities in our [User Guide](https://github.com/SaniyaKhullar/NetREm/blob/main/user_guide/).





## References

[1]: Caiyan Li, Hongzhe Li, Network-constrained regularization and variable selection for analysis of genomic data, Bioinformatics, Volume 24, Issue 9, May 2008, Pages 1175–1182, https://doi.org/10.1093/bioinformatics/btn081

[2]: Wilkinson, M.D., Dumontier, M., Aalbersberg, I.J. et al. The FAIR Guiding Principles for scientific data management and stewardship. Sci Data 3, 160018 (2016). https://doi.org/10.1038/sdata.2016.18

[3]: Zhang, Y., Akutsu, T., & Ching, W. K. (2016). Incorporating network topology and phylogenetic constraints for protein-protein interaction prediction. BMC bioinformatics, 17(1), 1-15. https://doi.org/10.1186/s12859-016-1310-4

[4]: Jia, C., Zhang, Y., Chen, K., & Zhang, S. (2018). A novel feature extraction method with improved consistency for identifying cell cycle regulated genes. Bioinformatics, 34(5), 896-903. https://doi.org/10.1093/bioinformatics/btx657

[5]: Lu, Y., Chen, X., & Hu, Z. (2017). Recognition of protein/gene names from text using an ensemble of classifiers and effective abbreviation resolution. BMC bioinformatics, 18(1), 1-11. https://doi.org/10.1186/s12859-017-1515-1

[6]: Pedregosa, F., Varoquaux, G., Gramfort, A., Michel, V., Thirion, B., Grisel, O., ... & Vanderplas, J. (2011). Scikit-learn: Machine learning in Python. Journal of Machine Learning Research, 12(Oct), 2825-2830.

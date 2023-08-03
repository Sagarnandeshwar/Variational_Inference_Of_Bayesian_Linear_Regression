# Variational inference of Bayesian polygenic risk score regression with spike-and-slab prior
Implemented the variational inference (VI) algorithm to infer Bayesian linear regression coefficients that follow a spike-and-slab prior and reparametrized into Bernoulli-Gaussian prior.

## Variational Inference 
Variational inference is a technique used in Bayesian statistics and machine learning to approximate complex posterior distributions when exact inference is computationally infeasible. It is particularly useful when dealing with models that involve high-dimensional latent variables or complex dependencies. 

## Polygenic risk score (PRS) regression 
Polygenic risk score (PRS) regression is a statistical method used in genetics to predict complex traits or diseases based on the aggregate effects of multiple genetic variants. It involves calculating a polygenic risk score for an individual by summing the effects of genetic variants, each weighted by its effect size. 

## A Spike and Slab prior 
A spike-and-slab prior is a type of prior distribution commonly used in Bayesian regression to handle variable selection. It consists of two parts: 
- **Spike:** This part of the prior assigns high probability mass to a specific value of the regression coefficient, often set to zero, representing the "sparsity" assumption, meaning that many coefficients are expected to be exactly zero. 
- **slab:** The slab part assigns a broader distribution to the coefficients, allowing them to take non-zero values. 
By combining the spike and slab components, the spike-and-slab prior encourages sparsity in the model, effectively performing variable selection by setting some coefficients to exactly zero. 


## Variational inference of Bayesian linear regression 
When applying variational inference to Bayesian polygenic risk score regression with a spike-and-slab prior, the main goal is to approximate the posterior distribution over the regression coefficients and other model parameters. This approximation is achieved by transforming the inference problem into an optimization problem. Variational inference optimizes a variational lower bound on the log marginal likelihood (evidence) of the model. 

In particular, the model is specified as below: 

![model](https://github.com/Sagarnandeshwar/Variational_Inference_Of_Bayesian_Linear_Regression/blob/main/image/model.png)

## The EM algorithm of Bayesian PRS with spike-and-slab prior 
![em](https://github.com/Sagarnandeshwar/Variational_Inference_Of_Bayesian_Linear_Regression/blob/main/image/em.png)

## Dataset: 
There are M = 100 SNPs, Ntrain = 439 training patients, Ntest = 50 test patients. Both the genotype Xtrain,Xtest and phenotype ytrain, ytest were standardized. The marginal summary statistics βmarginal saved in beta marginal.csv.gz and the LD matrix R saved in LD.csv.gz were completely derived from the 439 training patients only: 

## Expectation step 
In the Expectation step, we cycle through one SNP at a time to update its posterior precision and posterior mean of the effect size and its PIP while fixing all of the rest of the SNPs. 

Set the initial values for the posterior estimates for all SNPs to the following values: 
![e1](https://github.com/Sagarnandeshwar/Variational_Inference_Of_Bayesian_Linear_Regression/blob/main/image/e1.png)

Set the initial values for the hyperparameters to the following values: 
![e2](https://github.com/Sagarnandeshwar/Variational_Inference_Of_Bayesian_Linear_Regression/blob/main/image/e2.png)

For numerical stability, after a full cycle of E-step, cap the resulting PIP γj of each SNP within [0.01, 0.99]. That is, if γj < 0.01 set it to 0.01, and if γj > 0.99 set it to 0.99. 

## Maximization step 
In Maximization step, we didn't update τϵ in the M-step and keep its value always as 1, and only implement the update for τβ and π. 

## Evidence lower bound 
The evidence lower bound (ELBO) of the model is: 
![elbo](https://github.com/Sagarnandeshwar/Variational_Inference_Of_Bayesian_Linear_Regression/blob/main/image/elbo.png)

More specifically, 

![elbos](https://github.com/Sagarnandeshwar/Variational_Inference_Of_Bayesian_Linear_Regression/blob/main/image/elbos.png)

where γ∗j , μ∗j , and τj are the inferred PIP, mean and precision of the effect size for SNP j at the E-step, respectively; ◦ is the elementwise product of two vectors. 

I ran EM-update algorithm for 10 iterations and the plot the ELBO as a function of iterations. 
![elbor](https://github.com/Sagarnandeshwar/Variational_Inference_Of_Bayesian_Linear_Regression/blob/main/image/elbor.png)

## Evaluating PRS prediction 
Xtrain, ytrain, Xtest, ytest from the respective CSV files: X train.csv.gz, y train.csv.gz, X test.csv.gz, y test.csv.gz 

Then, predict PRS for both the 439 training patients and the 50 testing patients using their genotypes: 
![train](https://github.com/Sagarnandeshwar/Variational_Inference_Of_Bayesian_Linear_Regression/blob/main/image/train.png)

where γ∗ and μ∗ are the inferred PIP and expected effect size at the E-step, respectively. ◦ is the elementwise product of two vectors. 

I then calculated the Pearson correlation coefficient (PCC) between your predicted phenotypes and the true phenotypes, and generated the following scatter plots PRS prediction on training and testing set

![prs1](https://github.com/Sagarnandeshwar/Variational_Inference_Of_Bayesian_Linear_Regression/blob/main/image/prs1.png)
![prs2](https://github.com/Sagarnandeshwar/Variational_Inference_Of_Bayesian_Linear_Regression/blob/main/image/prs2.png)

## Fine-mapping 
Inferred PIP γ. The causal SNPs are rs9482449, rs7771989, and rs2169092 are colored in red. 

![pip](https://github.com/Sagarnandeshwar/Variational_Inference_Of_Bayesian_Linear_Regression/blob/main/image/pip.png)

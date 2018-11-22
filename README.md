# PictureSensation - a mobile application to help the blind explore the visual world through touch and sound

 ## About
 METACLUSTER facilitates comprehensive condition and tissue-specific expression analysis of metabolic gene clusters based on a probabilistic framework for characterizing metabolic gene clusters using context-specific gene expression information
 
  ![Alt text](/figure1.jpg?raw=true "functionality map")
 A) The METACLUSTER framework. B) Cluster diagram and transcriptional activity map of the arabidiol/baruol cluster (Yu et al. 2016) (C463 based on the prediction in Schlapfer et al. 2017). Colors indicate the inferred p-value of the cluster to be transcriptionally active per condition and tissue. Gray tiles indicate condition-tissue combinations that are missing in the differential expression dataset. C) Transcriptional activity map of the 317 inferred context-specific gene clusters. Color values denote the number of the transcriptionally active gene clusters per condition-tissue. Black tiles indicate condition-tissue combinations with no inferred transcriptionally active clusters.
 
 
 
 [Contact](mailto:mbanf.research@gmail.com) in case you've found a bug. 
 
 ## Installation
 
 `METACLUSTER` was build based on R version 3.4.3. The easiest way to install `METACLUSTER` is through `devtools` (see OS specific notes on installing devtools at the end)
 
 ```
 library(devtools)
 
 # depending on your system (Windows, Mac or linux) please use
 install_github("https://github.com/mbanf/METACLUSTER")
 # or
 install_github("METACLUSTER","mbanf")
 
 
 ```
 
 ## Usage
 
 To run the METACLUSTER with the Schlapfer et al. 2017 A.thaliana gene cluster predictions data you can download all neccessary datasets from onedrive: [datasets_athaliana](https://1drv.ms/u/s!Avm82Xhe9EZj4wRY7ZpGrZI-zvM9)
 
 set specific data format for "custom" files at the end
 
 ```
 library(METACLUSTER) # load package
 
 setwd("/User/home/METACLUSTER_athaliana_datasets") # set working directory to the dataset files
 
 
 ```
 
 Load datasets parameters:
 
 * `input_format` "custom", "PCF2017_enzymes_only" or "PCF2017" (default = "PCF2017_enzymes_only")
 * `filename.genes` genes (rows of the expression datasets)
 * `filename.experiment_ids` experimental datasets (columns of the expression datasets)
 * `filename.geneCluster` filename gene clusters
 * `filename.foldChange_differentialExpression` differential expression data (fold changes)
 * `filename.pvalue_differentialExpression`  differential expression data (p-values)
 * `filename.experiment_condition_tissue_annotation` experiment to treatment and tissue annotation
 
 ```
l.data = load_datasets(input_format = "PCF2017_enzymes_only",
                       filename.genes = "data/genes.txt",
                       filename.experiment_ids = "data/experiment_ids.txt",
                       filename.geneCluster = "data/ath_geneInCluster_3_aracyc.txt-labeled_NoHypoGenes.txt",
                       filename.foldChange_differentialExpression = "data/m.foldChange_differentialExpression.txt",
                       filename.pvalue_differentialExpression =	"data/m.pvalue_differentialExpression.txt",
                       filename.experiment_condition_tissue_annotation ="data/experiment_annotation.txt")
 ```
 
 METACLUSTER Parameter sets:
 
 !We set b.load_codifferentialAnalysis_monteCarloSimulation = "yes" for the Schlapfer et al. 2017 A.thaliana gene cluster predictions data, as we have pre-computed and provided all co-differential expression datasets - for other datasets, set to "no"!
 
 
 * `m.foldChange_differentialExpression` differential expression foldchange matrix - rows are genes, cols are experiments
 * `m.pvalue_differentialExpression` differential expression pvalue matrix - rows are genes, cols are experiments
 * `df.experiment_condition_annotation` experiment condition annotation
 * `df.geneCluster` gene cluster dataset
 * `tb.condition_treatments` table of conditions
 * `tb.condition_tissues` table of tissues
 * `n.cpus` number of cores used
 * `b.load_codifferentialAnalysis_monteCarloSimulation` load codifferential expression data ("yes", "no")
 * `pvalue_DifferentialExpression` pvalue treshold for differential expession (default = 0.05)
 * `probability_codifferentialExpression_MonteCarloSimulation` probability threshold codifferential expression (default = 0.05)
 * `pvalue_coexpression_distribution` pvalue treshold context specific coexpression (default = 0.05)
 * `pvalue_geneClusterPrediction` pvalue gene cluster inference enzyme presence (default = 0.05)
* `pvalue_geneClusterConsistency` pvalue gene cluster enzyme condition consistency (default = 0.05)
* `pvalue_treatment_per_condition` pvalue gene pair condition annotation (default = 0.05)
* `pvalue_tissue_per_condition` pvalue gene pair tissue annotation (default = 0.05)
* `number_codifferentialExpression_MonteCarloSimulations` number of codiffernetial expression background monte carlo simulations (default = 1)
* `number_conditionSpecificCoexpressionBackgroundGenePairs` number of context specific coexpression simulation background gene pairs (default = 100)
 * `min_number_condition_samples` minimum number of condition samples for significance test (default 1)
 * `foldername.tmp` temp file folder name (default = /tmp)
 * `foldername.results` results file folder name (default = /results)
 
 ```
df.cluster_annotations = run_METACLUSTER(m.foldChange_differentialExpression = l.data$m.foldChange_differentialExpression,
                                        m.pvalue_differentialExpression = l.data$m.pvalue_differentialExpression,
                                        df.experiment_condition_annotation = l.data$df.experiment_condition_annotation,
                                        df.geneCluster = l.data$df.geneCluster,
                                        tb.condition_treatments = l.data$tb.condition_treatments,
                                        tb.condition_tissues = l.data$tb.condition_tissues,
                                        n.cpus = 3,
                                        b.load_codifferentialAnalysis_monteCarloSimulation = "yes",
                                        pvalue_DifferentialExpression = 0.05,
                                        probability_codifferentialExpression_MonteCarloSimulation = 0.95,
                                        pvalue_coexpression_distribution = 0.05,
                                        pvalue_geneClusterPrediction = 0.05,
                                        pvalue_geneClusterConsistency = 0.05,
                                        pvalue_treatment_per_condition = 0.05,
                                        pvalue_tissue_per_condition = 0.05,
                                        number_codifferentialExpression_MonteCarloSimulations = 1,
                                        number_conditionSpecificCoexpressionBackgroundGenePairs = 100,
                                        min_number_condition_samples = 1,
                                        seed = 1234,
                                        heatmap_width = 10,
                                        heatmap_height = 5,
                                        foldername.results = "results/",
                                        foldername.tmp = "tmp/")
 ```
 
 Next evaluate and store the results
 ```
evaluate_and_store_results(df.cluster_annotations=df.cluster_annotations,
                           df.experiment_condition_annotation = l.data$df.experiment_condition_annotation,
                           tb.condition_treatments = l.data$tb.condition_treatments,
                           tb.condition_tissues = l.data$tb.condition_tissues,
                           min_number_of_genes = 3,
                           heatmap_width = 4, heatmap_height = 7, fontsize = 7, fontsize_row = 10, fontsize_col = 10,
                           foldername.results = "results/")

 ```
 
 ## Notes
 
 Installation of devtools dependencies under Ubuntu (prior to installing devtools):
 sudo apt-get install build-essential libcurl4-gnutls-dev libxml2-dev libssl-dev
 
 Then install.packages("devtools")
 
 Custom gene cluster data format:  "Cluster.ID", "Gene.ID", "Gene.Name", see [custom_example_data](https://1drv.ms/t/s!Avm82Xhe9EZj4wVPMoWIubwiA9uI)


 

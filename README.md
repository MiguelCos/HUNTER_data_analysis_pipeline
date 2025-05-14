# HUNTER data analysis workflow with RStudio and Quarto reporting

This repo will hold scripts and reproducilbe reports for the initial steps of data processing and analysis of HUNTER experiments. The general workflow is based on the TermineR approach with specific modifications for terminally enriched samples. 

## Basic information

The .qmd files are ‘reproducible report’ scripts aimed for the exploratory and inferential analysis of HUNTER experiments after a search with FragPipe-DIA + DIANN quantitation. 

The scripts are organized so the user can define input files and analysis parameters within the first section of the script, and then execute the code and analysis with minimal modifications in the code.  

- `preHUNTER_labelling_efficiency_analysis_refined.qmd`: contains code to load the psm.tsv file(s) from a FragPipe search and evaluate labelling efficiency by number of ‘taggable sites’ or by number of PSMs and respective modifications.

- `HUNTER_exploratory_refined.qmd`: contains code for basic exploratory visualizations: number of identifications by N-terminal modification, missing value distribution, distribution of abundances (before and after imputation), and principal component analysis (PCA).

- `HUNTER_inferential_analysis_refined.qmd`: contains code for the execution of pre-processing and differential abundance analysis after filtering for specific features, i.e., when you want to proceed with your analysis only focusing on N-terminally dimethylated peptides, or exclude those with preceding R. Then it performs a two-step imputation: completely missing features are imputed by sampling out from a gaussian distribution of minimal values, and partially missing features are imputed by impSeqRob (definition of completely and partially missing features can be controlled by the user as explained later in this guide. It generates a tabular output with limma results by contrast and feature annotation(specificity, preceding amino acids, cleavage window, uniprot processing information, etc.). It excludes features that are completely missing in 1 or more conditions.

## Requirements and Installation 

You need to have the following installed in your computer (I am listing the newest versions of all of these programs, which at the time of writing of this documentation are working properly):

1.	R version >= 4.2 (https://cran.r-project.org/bin/windows/base/)
2.	Rstudio >= 2024.04.2 (https://posit.co/download/rstudio-desktop/)
3.	Quarto >= 1.5.57 (https://quarto.org/docs/get-started/)

In terms of R packages, all the required ones should be installed, if necessary, by the script.

**NOTE ‘error: xxxx function not found’:** If you receive this kind of error, most likely you are missing some package installation. If the error gives you any hint of the package that you are missing, install it by running ‘install.packages(“package_name”) or BiocManager::install(“package_name”). If you don’t know what package you might be missing, look at the section ‘Required packages’ of the script and then look in the ‘Packages’ tab in RStudio (low-right panel, third tab). If any package is missing from your installed packages but it is listed in the required packages it needs to be installed. 

Two important packages require installation from GitHub source code:

```
devtools::install_github("MiguelCos/TermineR")
```

```
devtools::install_github("vdemichev/diann-rpackage")
```

You might need to install the ‘devtools’ package before with install.packages(“devtools”).

### Experimental metadata/sample annotation file

You need a sample metadata/annotation file in .tsv format. It should contain AT LEAST the columns ‘sample’ and ‘condition’ (exactly this name). We have an example file in the folder: example_sample_metadata_annotation.tsv; you can modify it according to your experiment. Here I describe each column and what is expected to have.

- **sample**: should have the names of the raw files from the MS runs used for the FragPipe search, without the extension (i.e., without ‘.raw’). THIS IS THE MOST IMPORTANT COLUMN, the names should match exactly the names of your MS files as they went into FragPipe.
- **sample_name**: is a simplified and meaningful name for each sample. It should be different for each sample.
- **condition**: experimental condition.
- **bio_replicate**: only relevant if your experiment has some kind of repeated measures structure (samples from the same individual measured in different time points or conditions). If this is not the case, you can leave it all the same.
- **run**: same as sample.

## Runnning the scripts

### General usability notes

1.	Within your project folder (where you store your search results), define an RStudio project (check this guide: https://intro2r.com/rsprojs.html). That will define a location from which all the files will be looked for by your script.
2. Recommended: create some kind of structure in your project folder to organize your scripts and data (see example at: https://intro2r.com/dir_struct.html). 
3. Within that folder copy the qmd files for your data analysis (our HUNTER scripts)
   a. From this repo, you can download the scripts and put them in your project folder.
   b. To download the individual scripts, click on the `.qmd` file link, and then click on the ‘raw’ button. This will open the script in a new tab. You can then right-click and select ‘save as’ to download the script.
   c. You can also download the entire repo as a zip file and extract it in your project folder (see the green ‘Code’ button in the top right corner of this page).
4. You can create a results folder where you can put your tabular results from the scripts.
5. You can then open your qmd files by clicking on the RProj file in your project folder. This will open RStudio with your project and the scripts you downloaded. You can also open the scripts by clicking on them in the files tab of RStudio (bottom right panel).

**Important suggestion**

Rename your scripts files, and change the title so they are meaningful to you and the project they are associated with.

If you want to make modifications in the code of some scripts, I would recommend you generate a copy with the initial version.

### Labelling efficiency script


1.	Open the `preHUNTER_labelling_efficiency_analysis_refined.qmd` script from within your RStudio project.
2.	Navigate to the section ‘Define interesting variables’. This is where you can the modifications that are more interesting to you.
    a.	Change the input for the ‘analysis_path’ object. If you correctly set up your RStudio project, you can use a relative path to the folder containing your FragPipe search.
    b.	Modify each other input in this chunk according to the expected modification mass of your experiment. Keep the format for the script for the mass and N-terminal and K modification mass.
    c.	Modify the specificity of your experimental protease (“K|R” is trypsin).
3.	You can now continue executing each individual chunk in RStudio to generate the desired plots.

### Exploratory analysis script

1.	Open the `HUNTER_exploratory_refined.qmd` script from within your RStudio project.
2.	Navigate to the section ‘Define comparisons and paramters’ and modify the required input accordingly.
    a.	`diann_report_location`: can be defined as a relative path to the report.tsv file from your RProj file.
    b.	`location_annotation`: relative path to your sample metadata/annotation file (see above on how to create this file).
    c.	`fasta_location`: relative path to a fasta file containing at least all the protein sequences of proteins identified in your FragPipe search. All FragPipe searches usually include a ‘protein.fas’ file. You can also use the UniProt fasta file for your organism of interest. Note: The pipeline only works with UniProt type of sequences and annotations
    d.	`sense_protease`: “C” if your experimental protease cuts and the carboxylic side (like trypsin), “N” if it cuts at the aminic side.
    e.	`specificity_protease`: same as described for the labelling efficiency script (“K|R” is trypsin).
    f.	`organism_annotation`: definition of your organism of interest. Options: "mouse", "human", "medicago_trucantula", "rhizobium_melitoli", "pig", "human_iso", "ecoli" and "arabidopsis".
    g.	`instrument`: should match the initials that are used for the naming of our MS files. “EX” for exploris, “QE” for Q-exactive… 
    h.	`missing_accepted`: should be defined as the proportion of missing values accepted by the user. Example: `1 / 4` would mean that a feature can be missing in up to 1 out of 4 replicates per condition. If it is missing in more than 1 replicate, it would be considered as 'completeley missing' in one condition.
    i.	`tune_sigma` and tune_quantile: tune sigma is recommended to leave as is, and tune_quantile should be set as a very low number < `0.0001`. Specially if you are interested in seen differential abundance of features that are already lowly abundant.
3.	Execute each chunk individually and or generate your report by direct rendering. You shouldn’t need to modify anything else unless you want to modify aesthetics or add other visualizations.

### Inferential analysis script

1.	Open the `HUNTER_inferential_analysis_refined.qmd` script from within your RStudio project.
2.	Navigate to the section “Required R packages”.
    a.	In the ‘Variable’ subsection, define your organism database R package:


- `library(org.Sc.sgd.db)` for yeast
- `library(org.Hs.eg.db)` for human 
- `library(org.Mm.eg.db)` for mouse
- `library(org.Rn.eg.db)` for rat
- `library(org.At.tair.db)` for Arabidopsis
- others available at: https://bioconductor.org/packages/3.15/data/annotation/.
    b.	NOTE: you might need to install your required package (you will know if you get an error running the library command). 

3.	Navigate to the section “Define comparisons and parameters”, and modify accordingly. You can follow point 2 described in the section Exploratory Analysis Script of this documentation until instrument.
    a.	`defined_contrasts`: here you need to define the comparisons you want to make with your limma analysis. In the example below, the left side of the equal sign defines an arbitrary name you give to the contrast, and “Oct1 – WT” on the right side, define what’s need to be compared. These need to match exactly the name of your conditions as defined in your experiment metadata/annotation file. 

```
c(
  KO_vs_WT = "KO - WT"
)
```

You can define more than one contrast if you have more than 1 condition, limma will run each contrast and each volcano will be visualized side by side for each.

```
c(
  KO1_vs_WT = "KO1 - WT",
  KO1_vs_KO2 = "KO1 - KO2"
)
```

    b. `fc_threshold` and `pval_threshold`. Modify according to your experiment. Normally pval threshold be 0.05.
    c.	`pre_fix`: here you would define a prefix for the output files generated by the script. Normally it should be meaningful for your experiment.
    d. `keep_only`: here you would list the modifications that you would consider for your final HUNTER analysis. "Dimethyl", "Acetyl" means that you would keep only features that contain either dimethyl or acetyl at the N-termini.
    e. `exclude_preceding`: if your undecanal pullout efficiency is not so great (you have a lot of labelled peptides that potentially come from tryptic digestion; you see that after the exploratory script and the labelling efficiency script), you can exclude peptides that have “R” as a preceding amino acid. Change to NULL (without quotation marks), if you don’t want to exclude features by their preceding amino acid.

4.	You should not need to change anything from the script until the section “Visualization of differential abundance analysis”.
    a.	Here, in the subsections ‘Basic’ and ‘Labelled’ volcano, you should change the section of the code that controls the labels of the plot in the ‘labs’ function.

5.	You might get errors in the section “Functional annotation of 'substrates”.
    a.	The main reason is that within the chunks it evaluates the presence of intermediary files in a specific folder that it expects to find within your RStudio folder. `r_project_files/rds/…` If you create these folder structure as recommended in the usability notes, this should be solved.

6.	After the section Heatmaps of cleavage areas of differentially abundant termini, you might want to change the titles of the following subsections, and you will need to change the definition of the `contrast` that you want to highlight in your cleavage area heatmap.
    a.	You might also want to change the title of the heatmaps in the argument `main` within the pheatmap function.

7.	In this section, we are filtering for interesting features (i.e., upregulated in a particular contrast) and plotting the cleavage areas using a heatmap. You can generate different heatmaps based on your features of interest. 

8.	In the section Sequence logos of cleavage areas of differentially abundant termini, we also extract sequences of cleavage areas and visualize them using sequence logos.
    a.	For sequence logos, we need to build a background model with sequences of the organism of interest. 
    b.	You would need to define the organism in the `species` argument within the function `prepareProteome` in the first chunk.
    c.	Following code should work with the same input of sequences generated for the heatmaps.

9.	In the sections for the lollipop plots and the heatmaps of top features, we are also filtering for a subset of features to showcase. You can modify the input objects at this point by filtering to keep features that you want to showcase, but the by default the script will be showcasing the top hits by adjusted p-values.

10.	In the section ‘Save tabular results’ you need to modify the input for the `file_name` object, so it matches the structure of your project folder and the location where you will place your results files. Example: if you followed the recommendation at step 2 of the subsection ‘General usability notes’, you have created a folder `r_project_files` and a `results` folder within it. Then it would match the input `paste0("r_project_files/results/",pre_fix, "_imputation_summary_table.tsv")` that would create a tabular result within the results folder.


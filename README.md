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

```
You might need to install the ‘devtools’ package before with install.packages(“devtools”).
```

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
2. Recommended: create some kind of structure in your project folder to organize your scripts and data, in the example you can see I created a r_project_files folder within the general project folder. 
3. Within that folder copy the qmd files for your data analysis (our HUNTER scripts)
   a. From this repo, you can download the scripts and put them in your project folder.
4. You can create a results folder where you can put your tabular results from the scripts.
5. You can then open your qmd files by clicking on the RProj file in your project folder.

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

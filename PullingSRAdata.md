This is an example of pulling 16S rRNA data from NCBI SRA for use in an amplicon pipeline.


## Guidelines for Picking a Marine Animal Microbiome Dataset

1. Do a literature search.

  * Look for papers which have 16S rRNA Illumina sequencing data (usually sequenced on a sequencer called ‘MiSeq’
  * Look for appropriate host organisms 
  	* Let’s try to stick to shellfish [oysters, clams, lobsters, abalone, etc.] to start since there has already been a lot of work on those. Fish may be appropriate too. Marine mammals may be a bit tricky at this point.
  * Look at the sampling scheme. Having multiple samples over a temperature gradient is key. If there is information on disease status of the host organism, even better.
  * Look for where the datasets are deposited (an NCBI Bioproject is probably most useful for us at this point but we could potentially work with data in ENA or DDBJ too).
2. Look up dataset. NCBI example:
  * [NCBI](https://www.ncbi.nlm.nih.gov/) has search menu. Choose ‘Bioproject’ and put in bioproject ID.
  * The search should show you info about the study and have links to the paper. 
  * Under ‘Project Data’ there are likely ‘SRA Experiments’ and ‘Biosamples.’ These are the links to the actual fastq files and their associated metadata. 
	* If you click through ‘Biosamples’ you will see information that the authors deposited from the sample (like temperature). Make sure you can understand the author’s meaning and what the data are here (it may help to go back to the paper).
	* In ‘SRA’ you will see links to the actual fastq files and their sequencing parameters (such as the primers that were used and if they were paired end or not.). This is also important information to understand.
  * Based on the Biosample and SRA, you may decide whether or not this is a useful dataset for our hypothesis. If it is, then you can move on to the next steps.


## Getting SRA Data- local Conda installation

- This is written for Conda environment 
	- You can also use these tools without having Conda, by installing through Unix (Instructions [here](https://ncbi.github.io/sra-tools/install_config.html)) and also some tips [here](https://reneshbedre.github.io/blog/fqutil.html) and [here](https://bioinformaticsworkbook.org/dataAcquisition/fileTransfer/sra.html).
- For Conda, I found these [instructions](https://bio331.devbioinformatics.org/conda.html) for adding tools via command line helpful.


```
conda install -c bioconda sra-tools

```

Check by downloading example file

```
cd /Users/admin/Documents/Molloy/Research/MarineAnimalDisease 

fastq-dump -A "SRR7694205"
```

Worked! Come back [here](https://bioinformaticsworkbook.org/dataAcquisition/fileTransfer/sra.html) later to see how to deal with the `@SRR` lines if they become a problem.


Try with one of the datasets I'm interested in, BioProject 
PRJNA421986: The Pacific oyster, Crassostrea gigas, associated microbial community (went to SRA Run Selector and grabbed some of the SRR id's)

- Navigate into removeable hard drive since these are large files.
- Indicate that these are paired-end and download both reads

```
fastq-dump --split-files SRR6374533 
```


- Try multiple files at once

```
fastq-dump --split-files SRR6374534 SRR6374535
```

To get a list of all files, from BioProject page click on the SRA experiments > Send to> File> Format: Accession list. 

## Get sample metadata 
This is for making one file with all the contextual environmental data, like temperature, etc.


First install NCBI's Utilties (got this from [here](https://anaconda.org/bioconda/entrez-direct))

```
conda install -c bioconda entrez-direct
```

Then use esearch to pull biosample metadata (example from this [page](https://www.biostars.org/p/279582/))
```
esearch -db bioproject -query "PRJNA421986" | elink -target biosample | efetch -format docsum | xtract -pattern DocumentSummary -block Attribute -element Attribute > PRJNA421986_biosample_metadata.txt
```

Note that the file generated above does not have the SRA ids, just the sample ids. The SRA metadata (from the 'SRAruntable.txt') can be matched later to the biosample metadata table by sample ID in R (using left_join or something). To get the `SRAruntable.txt` from BioProject page: 

* Click SRA Experiments
* Click Send results to Run selector 
* Select all SRA experiments in table 
* In 'Select' Click Metadata under Total 
* Download 


## fastq-dump in the Discovery Environment

There is an app in Discovery Environment, NCBI-SRA-Fastq-dump-2.8.1, that puts fastq files from SRA directly into your Data Store. But it can only run one SRA file at a time. 
[Documentation](https://learning.cyverse.org/projects/cyverse-importing-sradata-q).

* Navigate to app
* For input, put accession of a single SRR file 
* Choose maximum for all computational resources
* Under Optional Parameters, choose  split file and compress output
* `sra_output` folder will go to analyses folder

NOTE- You can only use this to do one file at a time. Multiples won't work the same way they do in the command line

## Next Steps

Import fastq files into QIIME2 using the manifest format (since they don't have the "Cassava" names).

https://docs.qiime2.org/2020.2/tutorials/importing/

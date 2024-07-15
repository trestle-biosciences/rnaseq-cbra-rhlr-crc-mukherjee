# rnaseq-cbra-rhlr-crc-mukherjee
Follow the instructions below to reproduce all results published in the 2024 paper by Sampriti Mukherjee, "Combinatorial control of Pseudomonas aeruginosa biofilm development by quorum-sensing and nutrient-sensing regulators"

# Prereqs 

## Docker
The AWS pipelines and subsequent custom analyses will use Docker containers to ensure reproducibility of the results. To install docker, follow the instructions at the link below.

https://docs.docker.com/engine/install/

## Nextflow
Pipelines from nf-core use the program Nextflow to coordinate all the different computational steps. To install Nextflow, follow the instructions at the link below. 

https://www.nextflow.io/docs/latest/install.html

## AWSCLI (If Running on AWS)
The pipeline can be run using storage on AWS S3 buckets and AWS EC2 computational resources (through AWS Batch). In order to use these resources, you will need the AWS CLI installed, as well as credentials to use any specified buckets or computational resources. 

https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html#getting-started-install-instructions

# Configure the Run
When this analysis was originally run, all pipelines were run on AWS Batch while all secondary analyses were done locally.  

If running locally, make the following changes to the run:
1. In the file rnaseq-cbra-rhlr-crc-mukherjee.config, change the line `process.executor = 'awsbatch'` to `process.executor = 'local'`.  
2. Change directories referenced in the nextflow commands below to local directories. These can be relative directories.   

```
export SRA_DOWNLOAD_DIR="s3://mukherjee-lab/rnaseq-cbra-rhlr-crc-mukherjee/raw/"
export ASSETS_DIR="assets"
export REFERENCES_DIR="references"
export RNASEQ_RESULTS_DIR="s3://mukherjee-lab/rnaseq-cbra-rhlr-crc-mukherjee/results/"
export WORK_DIR="s3://mukherjee-lab/rnaseq-cbra-rhlr-crc-mukherjee/work/"
```

# Run Everything

# Get SRA data

```
sudo nextflow run -r 1.11.0 nf-core/fetchngs \
   -c rnaseq-cbra-rhlr-crc-mukherjee.config \
   -profile trestle \
   --nf_core_pipeline rnaseq \
   --nf_core_rnaseq_strandedness reverse \
   --input assets/ids.csv \
   --outdir $SRA_DOWNLOAD_DIR \
   -work-dir $WORK_DIR \
   -resume
```

Move the samplesheet to the assets folder (or bucket, if running on AWS):

Local Run:
`mv` 

AWS Batch:
`aws s3 cp` 

# Get Reference Files

If running locally, run the following code block to collect the 
```
cd references && \

wget https://pseudomonas.com/downloads/pseudomonas/pgd_r_22_1/Pseudomonas_aeruginosa_UCBPP-PA14_109/Pseudomonas_aeruginosa_UCBPP-PA14_109.gff.gz && \ 

wget https://pseudomonas.com/downloads/pseudomonas/pgd_r_22_1/Pseudomonas_aeruginosa_UCBPP-PA14_109/Pseudomonas_aeruginosa_UCBPP-PA14_109.fna.gz && \

gunzip Pseudomonas_aeruginosa_UCBPP-PA14_109.fna.gz && \

gunzip Pseudomonas_aeruginosa_UCBPP-PA14_109.gff.gz && \

cd ..
```
If running on AWS, after running the scripts above, move the reference files to an S3 bucket. 

```
aws s3 cp 
```


# Add crcZ to Annotations and Clean Files
Use gffutils to add crc to the gff file and clean up some names to prepare for the nf-core pipeline.

`docker exec container python clean_gff.py`

# Running the nf-core RNA-seq pipeline


# Running the Secondary Analysis
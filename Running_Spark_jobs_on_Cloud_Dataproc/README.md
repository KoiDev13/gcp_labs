# Running Apache Spark jobs on Cloud Dataproc

This lab source is coming from qwiklabs.com. [Resource](https://www.qwiklabs.com/focuses/674?locale=pt_BR&parent=catalog)

Thanks for making this wonderful tutorial :grinning:


## Overview
In this lab you will learn how to migrate Apache Spark code to Cloud Dataproc. You will follow a sequence of steps progressively moving more of the job components over to GCP services:

- Run original Spark code on Cloud Dataproc (Lift and Shift)
- Replace HDFS with Cloud Storage (cloud-native)
- Automate everything so it runs on job-specific clusters (cloud-optimized)

## Objectives
In this lab you will learn how to:

- Migrate existing Spark jobs to Cloud Dataproc
- Modify Spark jobs to use Cloud Storage instead of HDFS
- Optimize Spark jobs to run on Job specific clusters

## Lab setup

Clone the Git repository for the lab enter the following command in Cloud Shell:
```
git -C ~ clone https://github.com/GoogleCloudPlatform/training-data-analyst
```
To locate the default Cloud Storage bucket used by Cloud Dataproc enter the following command in Cloud Shell:
```
export DP_STORAGE="gs://$(gcloud dataproc clusters describe sparktodp --region=us-central1 --format=json | jq -r '.config.configBucket')"
```
To copy the sample notebooks into the Jupyter working folder enter the following command in Cloud Shell:
```
gsutil -m cp ~/training-data-analyst/quests/sparktobq/*.ipynb $DP_STORAGE/notebooks/jupyte
```
## Part 1: Lift and Shift
- [Spark Local](Running_Spark_jobs_on_Cloud_Dataproc/dataproc-staging-us-central1-652961163952-wrwamidq/../../dataproc-staging-us-central1-652961163952-wrwamidq/notebooks_jupyter_01_spark.ipynb)

## Part 2: Separate Compute and Storage
- Modify Spark jobs to use Cloud Storage instead of HDFS
  
  In the Cloud Shell create a new storage bucket for your source data.
  ```
    export PROJECT_ID=$(gcloud info --format='value(config.project)')
    gsutil mb gs://$PROJECT_ID
  ```
    In the Cloud Shell copy the source data into the bucket.
  ```
  wget https://archive.ics.uci.edu/ml/machine-learning-databases/kddcup99-mld/kddcup.data_10_percent.gz

  gsutil cp kddcup.data_10_percent.gz gs://$PROJECT_ID/
  ```

- Making the file notebooks_jupyter_De-couple-storage.ipynb
- [Decouple storage](Running_Spark_jobs_on_Cloud_Dataproc/dataproc-staging-us-central1-652961163952-wrwamidq/../../dataproc-staging-us-central1-652961163952-wrwamidq/notebooks_jupyter_De-couple-storage.ipynb)

## Part 3: Deploy Spark Jobs
- Optimize Spark jobs to run on Job specific clusters.
- Run the Analysis Job from Cloud Shell.
    
    Switch back to your Cloud Shell and copy the Python script from Cloud Storage so you can run it as a Cloud Dataproc Job.
    ```
    gsutil cp gs://$PROJECT_ID/sparktodp/spark_analysis.py spark_analysis.py
    ```
    Create a launch script
    ```
    nano submit_onejob.sh
    ```
    Paste the following into the script:
    ```
    #!/bin/bash
       gcloud dataproc jobs submit pyspark \
       --cluster sparktodp \
       --region us-central1 \
       spark_analysis.py \
       -- --bucket=$1
    ```
    Make the script execuatable 
    ```
    chmod +x submit_onejob.sh
    ```
    Launch the PySpark Analysis job
    ```
    ./submit_onejob.sh $PROJECT_ID
    ```
- [Pyspark-analysi-file](Running_Spark_jobs_on_Cloud_Dataproc/dataproc-staging-us-central1-652961163952-wrwamidq/../../dataproc-staging-us-central1-652961163952-wrwamidq/notebooks_jupyter_PySpark-analysis-file.ipynb)

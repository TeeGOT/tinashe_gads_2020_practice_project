# Lab: Google Cloud Fundamentals: Getting Started with BigQuery

## Objectives:
In this lab you will do the following:
 - load a web server log into a BigQuery table using BigQuery CLI
 - After loading the data, you query it using BigQuery CLI.

## Task 1: Log into the GCP console

## Task 2: Load data from Cloud Storage into BigQuery
1. Create a new dataset within your project 
 - (leaving the project name unspecified will default to your project) set *logdata* as Dataset ID 
 - For Data location, select the continent closest to the region your project was created in (in this case we use the *US* region)

        bq --location=US mk -d logdata

2. Create a new table named acesslog in the logdata
 - here you will store data from a CSV file in Google Cloud Storage with the URI *gs://cloud-training/gcpfci/access_log.csv*
 - The *--autodetect* flag utomatically detects the schema. *--source_format* tag specifies the format of the source file.

        bq load --autodetect --source_format=CSV logdata.accesslog gs://cloud-training/gcpfci/access_log.csv 

>Each row in this table logs a hit on a web server. The first field, string_field_0, is the IP address of the client. The fourth through ninth fields log the day, month, year, hour, minute, and second at which the hit occurred. In this activity, you will learn about the daily pattern of load on this web server.

## Task 3: Perform a query on the data
Note: *Because you told BigQuery to automatically discover the schema when you load the data, the hour of the day during which each web hit arrived is in a field called int_field_6.*

    bq query "select int64_field_6 as hour, count(*) as hitcount from logdata.accesslog group by hour order by hour"

From the query result,  at what time of day is the website busiest? When is it least busy?
Second query:

    bq query "select string_field_10 as request, count(*) as requestcount from logdata.accesslog group by request order by requestcount desc"
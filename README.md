# Microsoft_Fabric_Project
This project carries out a version of an existing project, which connects a Medicare dataset with 198 million rows to a report in Power BI. Additionally, it develops a Medallion architecture with Pyspark notebooks that incorporates optimizations to improve the reading speed and load time of the report. Original project: https://github.com/isinghrana/fabric-samples-healthcare/tree/main/analytics-bi-directlake-starschema

**1- Loading the datasets on the Microsoft Fabric Lakehouse and Bronze layer construction.**

  The first step to perform the  load on the Fabric Lakehouse of the collection of datasets is to download the files from the webpage of Medicare: https://data.cms.gov/provider-summary-by-type-of-service/medicare-part-d-prescribers/medicare-part-d-prescribers-by-provider-and-drug
  Once all the files are loaded on the lakehouse, it's time to craft the first medallion layer of the project's architechture, the bronze layer. The original structure of the files is maintained and the focus of the bronze structure is to tracking the future changes on the source dataset with the delta log and to define the data types of the columns. 
  All this process is developed by a pyspark notebook named *Bronze_Medicare*
- > The dataset used for this project consists of yearly files that are already closed and will not undergo changes over time. However, I have applied improvements to the original project such as the incorporation of timestamps and other implementations aimed at updating and inserting new data, which will not be carried out since the data will not be updated. It is structured in this way to practice building this Medallion architecture rather than for functional purposes.

**2- Silver layer construction**

  On an enterprise enviroment, the silver layer it's designed to be the central repository of the data. In this case, there was minimal transformations like concat the prescriber's city column and the Prescriber's state abreviation. The pyspark notebook is designed to do the first load if the table was never loaded before. It takes the directory table names of the bronze delta tables and append all together in one large table that will be the central repository. The other part of the notebook dettect if the silver table alredy exist, update the values that was changed since the last load and insert the ones that wasn't included. 
  
  Considering that the silver layer is going to be oftenly written, the notebook include a few optimization techniques to enhance the future loads. 
  - *Autotune file size based on workloads*:   This feature optimize the writting of data in fewer files addapting the threshold for the data compaction to the file size. Applied in the update statement to accelerate write-intensive operations.
  - *Autocompaction for Delta Lake*: Combine small files to avoid write and read latency to increase. 
  - *Optimize writes for delta lake*: This feature is applied at Session_level in pyspark.This feature optimize the writting of data in fewer files with larger size to enhance the reading latency. It's a complement for autoCompact that only triggers for partitions or tables that have at least a certain number of small files. 
  - *VOrdering*:  Write-time optimization for enhance read performance. 


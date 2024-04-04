# Microsoft_Fabric_Project
This project carries out a version of an existing project, which connects a Medicare dataset with 198 million rows to a report in Power BI. Additionally, it develops a Medallion architecture with *Pyspark notebooks* that incorporates optimizations to improve the reading speed and load time of the report. Original project: https://github.com/isinghrana/fabric-samples-healthcare/tree/main/analytics-bi-directlake-starschema

**1- Loading the datasets on the Microsoft Fabric Lakehouse and Bronze layer construction.**

The first step to perform the load on the Fabric Lakehouse of the collection of datasets is to download the files from the Medicare webpage: https://data.cms.gov/provider-summary-by-type-of-service/medicare-part-d-prescribers/medicare-part-d-prescribers-by-provider-and-drug
Once all the files are loaded on the lakehouse, it's time to craft the first medallion layer of the project's architecture, the bronze layer. The original structure of the files is maintained, and the focus of the bronze structure is to track the future changes on the source dataset with the delta log and to define the data types of the columns.
All this process is developed by a *Pyspark notebook* named *Bronze_Medicare*.

The dataset used for this project consists of yearly files that are already closed and will not undergo changes over time. However, I have applied improvements to the original project such as the incorporation of timestamps and other implementations aimed at updating and inserting new data, which will not be carried out since the data will not be updated. It is structured in this way to practice building this Medallion architecture rather than for functional purposes.

**2- Silver layer**

On an enterprise environment, the silver layer is designed to be the central repository of the data. In this case, there were minimal transformations like concatenating the prescriber's city column and the prescriber's state abbreviation. The Pyspark notebook is designed to do the first load if the table was never loaded before. It takes the directory table names of the bronze delta tables and appends all together in one large table that will be the central repository. The other part of the notebook detects if the silver table already exists, updates the values that were changed since the last load, and inserts the ones that weren't included.

Considering that the silver layer is going to be often written, the notebook includes a few optimization techniques to enhance the future loads.

- *Autotune file size based on workloads*: This feature optimizes the writing of data in fewer files, adapting the threshold for the data compaction to the file size. Applied in the update statement to accelerate write-intensive operations.
- *Autocompaction for Delta Lake*: Combine small files to avoid write and read latency increase.
- *Optimize writes for Delta Lake*: This feature is applied at Session_level in *Pyspark*. It optimizes the writing of data in fewer files with larger size to enhance the reading latency. It's a complement for autoCompact that only triggers for partitions or tables that have at least a certain number of small files.
- *VOrdering*: Write-time optimization for enhanced read performance.

**3- Gold layer**

The gold layer maintains the structure of the original project. To enhance query performance and a good time response, the data is modeled with a star schema. It keeps the fact table with the main measures of the dataset: Total beneficiaries, total claims, total drug cost, etc. Then, the rest of the dataset is divided into 4 dimensions. The drug dimension that stores all the generic and regular drugs dispensed by Medicare, the provider dimension with all the data related to the healthcare workers that provide the drugs, and the year and geography dimensions. The two implementations that change from the original project are:

- Enabling on *Spark Session* level the auto-compact mode to adjust the file size.
- Applying *ZOrder optimization* on drug and provider dimensions to store together rows with similar values. One of the main benefits is to enable the data skipping reducing the amount of data read in the queries.

![Star_schema](https://github.com/Axlope97/Microsoft_Fabric_Project/assets/148786116/857dcdf6-e88a-4059-9bca-e85c4df2bde1)



**Visualization**
![Medicare_report](https://github.com/Axlope97/Microsoft_Fabric_Project/assets/148786116/5a00a0de-8860-4768-bace-ae13f5f18447)



**Resources**
- Data Warehousing Modeling Techniques and Their Implementation on the Databricks Lakehouse Platform: https://www.databricks.com/blog/2022/06/24/data-warehousing-modeling-techniques-and-their-implementation-on-the-databricks-lakehouse-platform.html
- Delta Lake table optimization and V-Order: https://learn.microsoft.com/en-us/fabric/data-engineering/delta-optimization-and-v-order?tabs=sparksql
- The need for optimize write on Apache Spark: https://learn.microsoft.com/en-us/azure/synapse-analytics/spark/optimize-write-for-apache-spark
- Configure Delta Lake to control data file size: https://learn.microsoft.com/en-us/azure/databricks/delta/tune-file-size
- VOrder vs ZOrder: https://community.fabric.microsoft.com/t5/General-Discussion/V-Order-amp-Z-Order/m-p/3750437




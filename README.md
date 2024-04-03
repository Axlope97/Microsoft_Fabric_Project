# Microsoft_Fabric_Project
This project carries out a version of an existing project, which connects a Medicare dataset with 198 million rows to a report in Power BI. Additionally, it develops a Medallion architecture with Pyspark notebooks that incorporates optimizations to improve the reading speed and load time of the report.

**1- Loading the datasets on the MF Lakehouse.**

  The first step to perform the  load on the Fabric Lakehouse of the collection of datasets is to download the files from the webpage of Medicare: https://data.cms.gov/provider-summary-by-type-of-service/medicare-part-d-prescribers/medicare-part-d-prescribers-by-provider-and-drug
  Once all the files are loaded on the lakehouse, it's time to craft the first medallion layer of the project's architechture, the bronze layer. The original structure of the files is maintained and the focus of the bronze structure is to tracking the future changes on the source dataset with the delta log and to define the data types of the columns. 
  All this process is developed by a pyspark notebook named *Bronze_Medicare*
- > The dataset used for this project consists of yearly files that are already closed and will not undergo changes over time. However, I have applied improvements to the original project such as the incorporation of timestamps and other implementations aimed at updating and inserting new data, which will not be carried out since the data will not be updated. It is structured in this way to practice building this Medallion architecture rather than for functional purposes.



# Analyzing data with Oracle SQL

## Introduction

In most real-world scenarios, queries against your data warehouse would normally involve the use of a data visualization tool such as Oracle Analytics Cloud (or 3rd party business intelligence products such as Qlik, Tableau, PowerBI, and so on, currently support Autonomous Data Warehouse). Within this part of the workshop we will use SQL commands to query our data using the built-in SQL Worksheet.  

**Note:** Your Autonomous Data Warehouse also comes complete with a built-in machine learning notebook tool which is launched from the tools menu on the console page. It is aimed at data scientists and data analysts and allows them to build machine learning models using PL/SQL, Python and/or R. This feature is explored in one of our other online labs for Autonomous Data Warehouse.

  *Autonomous Data Warehouse also provides 5 free licenses for Oracle Analytics Desktop, which is the desktop client version of Oracle Analytics Cloud. For more information about Oracle Analytics Cloud [click here](https://www.oracle.com/uk/business-analytics/analytics-cloud.html)*.

### Prerequisites

- You will need to have completed the previous labs in this workshop, shown in the Contents menu on the left.

Before starting to run the code in this workshop, we need to manage the resources we are going to use to query our sales data. You will notice that when you open SQL Worksheet, it automatically defaults to using the LOW consumer group - this is shown in the top right section of your worksheet.

  ![LOW consumer group shown in worksheet](images/3054194710.png " ")

**NOTE**: Autonomous Data Warehouse comes complete with three built-in consumer groups for managing workloads. The three groups are: HIGH, MEDIUM and LOW. Each consumer group is based on predefined CPU/IO shares based on the number of OCPUs assigned to the ADW. The basic characteristics of these consumer groups are:

* HIGH: A high priority connection service for reporting and batch workloads. Workloads run in parallel and are subject to queuing.
* MEDIUM: A typical connection service for reporting and batch workloads. Workloads also run in parallel and are subject to queuing. Using this service the degree of parallelism is limited to 4.
* LOW: A connection service for all other reporting or batch processing workloads. This connection service does not run with parallelism.

For more information about how to use consumer groups to manage concurrency and prioritization of user requests in Autonomous Data Warehouse, please click the following link: [Manage Concurrency and Priorities on Autonomous Database](https://docs.oracle.com/en/cloud/paas/autonomous-database/adbsa/manage-priorities.html#GUID-19175472-D200-445F-897A-F39801B0E953). If you want to explore this topic using a workshop, [click here](https://apexapps.oracle.com/pls/apex/dbpm/r/livelabs/view-workshop?wid=618) to launch the **Managing and Monitoring in Autonomous Database** workshop.

Change the consumer group by simply clicking the downward pointing arrow next to the word LOW, and from the pulldown menu select **HIGH**.

  ![Select the HIGH consumer group from the pulldown menu.](images/3054194709.png " ")    

### Get Started!
The next few labs are very comprehensive - consisting of many different types of analytic queries. Get started by going to the next lab:  [Analyzying Movie Sales Data](#next).

## Acknowledgements

* **Author** - Keith Laker, ADB Product Management
* **Contributors** -  Richard Green, Principal Developer, Database User Assistance
* **Last Updated By/Date** - Keith Laker, August 2, 2021

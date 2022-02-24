# Explore Data and Run Queries

## Introduction

This lab picks up where lab 3 left off.   We are going to explore in more detail the tables we created, load data with functions, and execute queries a python application.  

Estimated Time: 25 minutes

### Objectives

* Understand the different tables
* Read data with REST api
* Read data with a python application

### Prerequisites

* An Oracle Free Tier, Always Free, or Paid Cloud Account
* Connection to the Oracle NoSQL Database Cloud Service
* Working knowledge of bash shell
* Working knowledge of Python


## Task 1: Restart the Cloud Shell

1. Lets get back into the Cloud Shell.  From the previous lab, you may have minimized it in which case you need to enlarge it.  It is possible it may have become disconnected and/or timed out.   In that case, restart it.

    ![](./images/cloud-shell-phoenix.png)

2. Execute the following environment setup shell script in the Cloud Shell to set up your environment. If you close/open the Cloud Shell Console, please re-execute it.

    ```
    <copy>
    source ~/serverless-with-nosql-database/env.sh
    </copy>
    ```

## Task 2: Load Data and Examine It

The goal of this task is to understand the difference between the 2 data models used. The demoKeyVal table is a schema-less table, sometimes referred to as a JSON document that contains a primary key and a JSON column.  The demo table contains the primary key, several fixed columns and a JSON column.  Sometimes referred to as a fixed-schema. These tables are logically equivalent. Which data model you use depends on your business model.   Oracle NoSQL Database Cloud Service is extremely flexible in how you can model your data.   It is a true multi-model database service.

  ![](./images/capturemultimodel.png)

1. Install the Node.js application.  Execute in the Cloud Shell.

    ```
    <copy>
    cd ~/serverless-with-nosql-database/express-nosql
    npm install
    node express_oracle_nosql.js &
    </copy>
    ```
    **Note:** This will start the "express-oracle-nosql" application in the background.

2. After you complete step 1, you will see a message in the shell saying 'application running'

    ![](./images/appl-running.png)

    Hit the **'Enter' key** on your keypad to get the command line prompt back.   

3. Insert data into the demo table.   

  This will be done using a curl command to transfer data over the network to the NoSQL backend using the express_oracle_nosql application.  Execute in Cloud Shell.

  ```
  <copy>
  FILE_NAME=`ls -1 ~/BaggageData/baggage_data_file99.json`
  curl -X POST -H "Content-Type: application/json" -d @$FILE_NAME http://localhost:3000/demo
  FILE_NAME=`ls -1 ~/BaggageData/baggage_data_file9.json`
  curl -X POST -H "Content-Type: application/json" -d @$FILE_NAME http://localhost:3000/demo
  FILE_NAME=`ls -1 ~/BaggageData/baggage_data_file103.json`
  curl -X POST -H "Content-Type: application/json" -d @$FILE_NAME http://localhost:3000/demo
  FILE_NAME=`ls -1 ~/BaggageData/baggage_data_file2.json`
  curl -X POST -H "Content-Type: application/json" -d @$FILE_NAME http://localhost:3000/demo
  FILE_NAME=`ls -1 ~/BaggageData/baggage_data_file84.json`
  curl -X POST -H "Content-Type: application/json" -d @$FILE_NAME http://localhost:3000/demo
  </copy>
  ```
4.  Insert data into the demoKeyVal table.  Execute in Cloud Shell.

  ````
  <copy>
  FILE_NAME=`ls -1 ~/BaggageData/baggage_data_file99.json`
  curl -X POST -H "Content-Type: application/json" -d @$FILE_NAME http://localhost:3000/demoKeyVal
  FILE_NAME=`ls -1 ~/BaggageData/baggage_data_file9.json`
  curl -X POST -H "Content-Type: application/json" -d @$FILE_NAME http://localhost:3000/demoKeyVal
  FILE_NAME=`ls -1 ~/BaggageData/baggage_data_file103.json`
  curl -X POST -H "Content-Type: application/json" -d @$FILE_NAME http://localhost:3000/demoKeyVal
  FILE_NAME=`ls -1 ~/BaggageData/baggage_data_file2.json`
  curl -X POST -H "Content-Type: application/json" -d @$FILE_NAME http://localhost:3000/demoKeyVal
  FILE_NAME=`ls -1 ~/BaggageData/baggage_data_file84.json`
  curl -X POST -H "Content-Type: application/json" -d @$FILE_NAME http://localhost:3000/demoKeyVal
  </copy>
  ````
5.  Read back the data that we just entered.  Execute in the Cloud Shell.  In the second two queries, we use a limit clause which limits the number of rows returned.  We also use an orderby clause to sort the returned results.

  ````
  <copy>
  curl -X GET http://localhost:3000/demo  | jq
  </copy>
  ````

  ````
  <copy>
  curl  "http://localhost:3000/demo?limit=3&orderby=ticketNo"  | jq
  </copy>
  ````

  ````
  <copy>
  curl  "http://localhost:3000/demo?limit=12&orderby=fullName"  | jq
  </copy>
  ````
6. Read Data for a specific TicketNumber using GET command.  Execute in Cloud Shell.

  ````
  <copy>
  curl -X GET http://localhost:3000/demo/1762322446040  | jq
  </copy>
  ````
7. In the baggage tracking demo from Lab 1, which is running live in all the regions, a Node.js application was running on the background.   We can install that application, and run it on our data.  It uses a different port number than the previous application we installed.  It will also run in the background, so **hit 'Enter'** to get the prompt back.  Execute in Cloud Shell.

  ````
  <copy>
  cd ~/serverless-with-nosql-database/express-nosql
  npm install
  node express_baggage_demo_nosql.js &
  </copy>
  ````

8. We can run a query by ticket number and passengers on a flight.  Execute in Cloud Shell.

  ````
  <copy>
  curl -X GET http://localhost:3500/getBagInfoByTicketNumber?ticketNo=1762322446040  | jq
  </copy>
  ````

  ````
  <copy>
  curl -X GET http://localhost:3500/getBagInfoByTicketNumber  | jq
  </copy>
  ````

  ````
  <copy>
  curl -X GET http://localhost:3500/getBagInfoByTicketNumber | jq '. | length'
  </copy>
  ````

  ````
  <copy>
  curl -X GET http://localhost:3500/getPassengersAffectedByFlight?flightNo=BM715  | jq
  </copy>
  ````
  Each of these produced slightly different results. The first one display the document with a specific ticket number, the second displayed all the records and the third gave a count of the records.

  For the last one,  you can see in the field "message" the getPassengersAffectedByFlight endpoint is still under construction. In other words the code for that endpoint has not been completed yet.

9. You can also execute sql statements using OCI CLI commands.  Going this route, you will be querying the data over REST.  Execute in Cloud Shell.

    ````
    <copy>
    SQL_STATEMENT=$(cat ~/serverless-with-nosql-database/objects/query1.sql | tr '\n' ' ')
    echo "$SQL_STATEMENT"
    </copy>
    ````

    ````
    <copy>
    oci nosql query execute -c  $COMP_ID --statement "$SQL_STATEMENT"
    </copy>
    ````
  In this case, the data is formatted as a nice JSON document.


## Task 3: Read Data Using a Python CLI Application

1. Create the python CLI application in the Cloud shell.  Execute in Cloud Shell.

    ```
    <copy>
    source ~/serverless-with-nosql-database/env.sh
    cd ~/serverless-with-nosql-database/
    pip3 install borneo
    pip3 install cmd2  
    </copy>
    ```

    ```
    <copy>
    python3 nosql.py -s cloud -t $OCI_TENANCY -u $NOSQL_USER_ID -f $NOSQL_FINGERPRINT -k ~/NoSQLLabPrivateKey.pem -e https://nosql.${OCI_REGION}.oci.oraclecloud.com
    </copy>
    ```
2.  This will create a Pyhton NoSQL shell that you can load data or execute queries in.

   ![](./images/capturepython.png)


3. Load additional data so we can run some queries.  Execute in Cloud Shell.

    ````
    <copy>
    load ../BaggageData/load_multi_line.json demo
    </copy>
    ````

4. Execute the following queries.  Execute in Cloud Shell.

    ````
    <copy>
    SELECT *
    FROM demo d
    WHERE d.bagInfo.flightLegs.flightNo =ANY 'BM715';
    </copy>
    ````

    ````
    <copy>
    SELECT d.fullName, d.contactPhone, d.ticketNo , d.bagInfo.flightLegs.flightNo as bagInfo
    FROM demo d
    WHERE d.bagInfo.flightLegs.flightNo =ANY 'BM715';
    </copy>
    ````

    ````
    <copy>
    SELECT d.fullName, d.contactPhone, d.ticketNo , d.bagInfo.flightLegs.flightNo as bagInfo
    FROM demo d
    WHERE d.bagInfo.flightLegs.flightNo =ANY "BM715"
    AND d.bagInfo.flightLegs.flightNo =ANY "BM204";
    </copy>
    ````

    ````
    <copy>
    SELECT d.fullName, d.contactPhone, d.ticketNo , d.bagInfo.flightLegs.flightNo as bagInfo
    FROM   demo d
    WHERE  d.bagInfo.flightLegs.flightNo =ANY "BM715"
    AND    d.bagInfo.flightLegs.flightNo =ANY "BM204"
    AND    size(d.bagInfo.flightLegs) = 2;
    </copy>
    ````

5. Write queries to answer the following questions. {MJB: need to complete questions}

  * How many xxx
  * Which passenger xxx
  * how many bags on flightNo xxx

    **Note:** The Learn More contains a link to the SQL Reference Guide.

6. Type in **exit** to exit from the python application.

7. Minimize the Cloud Shell by hitting the minimize key.

## Task 4: Clean Up

This task deletes the tables that got created.

1. On the top left, go to menu, then Databases, then under Oracle NoSQL Database, hit 'Tables'
Set your compartment to 'demonosql'
Click on the freeTest table, which will bring up the table details screen.  Hit Delete.

  ![](./images/delete-freetable.png)

  Deleting tables is an async operation, so you will not immediately see the results on the OCI console.  Eventually the status of the tables will get changed to deleted.  

2. Return to the 'Tables' screen and repeat the process for the demo and demoKeyVal tables.


## Learn More


* [Oracle NoSQL Database Cloud Service page](https://www.oracle.com/database/nosql-cloud.html)
* [About Oracle NoSQL Database Cloud Service](https://docs.oracle.com/pls/topic/lookup?ctx=cloud&id=CSNSD-GUID-88373C12-018E-4628-B241-2DFCB7B16DE8)
* [Java API Reference Guide](https://docs.oracle.com/en/cloud/paas/nosql-cloud/csnjv/index.html)
* [Node API Reference Guide](https://oracle.github.io/nosql-node-sdk/)
* [Python API Reference Guide](https://nosql-python-sdk.readthedocs.io/en/latest/index.html)
* [NoSQL SQL Reference Manual](https://docs.oracle.com/en/database/other-databases/nosql-database/21.2/sqlreferencefornosql/sql-reference-guide.pdf)
* [About Cloud Shell](https://docs.oracle.com/en-us/iaas/Content/API/Concepts/cloudshellintro.htm)


## Acknowledgements
* **Author** - Dario Vega, Product Manager, NoSQL Product Management and Michael Brey, Director, NoSQL Product Development
* **Last Updated By/Date** - Michael Brey, Director, NoSQL Product Development, September 2021

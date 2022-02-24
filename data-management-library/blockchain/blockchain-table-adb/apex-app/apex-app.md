# Import the Web Application and Test the Blockchain Table Features

## Introduction

In the lab, you will create an APEX workspace, define the rest end points and enable ORDS for the workspace. Then import the APEX application and run the application to test the blockchain functionality.

Estimated time: 20 minutes

### Objectives

In this lab, you will:

- Create APEX Workspace
- Define REST end points for the workspace
- Import an APEX application
- Run the APEX application and test the blockchain functionality

### Prerequisites

This workshop assumes you have:

- All previous labs completed.

## Task 1: Create APEX Workspace

1. Navigate back to the tab with Oracle Cloud console. Click on the hamburger menu, search for **Oracle Database** and click **Autonomous Transaction Processing**.

	![](./images/task1-1.png " ")

2. Click on the Display Name of your ADB instance to navigate to your ADB instance details page. In this lab, click on the provisioned **DEMOATP** instance.

	![](./images/task1-2.png " ")

3. Within your database, APEX is not yet configured. Therefore, when you first access APEX, you will need to log in as an APEX Instance Administrator to create a workspace.

    Click the **Tools** tab. Click **Open APEX**.
	![](./images/task1-3.png " ")

4. Enter the password for the Administration Services and click **Sign In to Administration**. The password is the same as the one entered for the ADMIN user when creating the ATP instance.

    In the lab, provide the **password - WElcome123##** for the ADMIN user you created when you provisioned your ADB instance and click **Sign in to Administration** to sign in to APEX Workspace.
	![](./images/task1-4.png " ")

5. Click **Create Workspace**.

	![](./images/task1-5.png " ")

6. In the Create Workspace dialog, enter the following and click **Create Workspace**.
    - Database User: DEMOUSER
    - Password : WElcome123## *Note:* The password field will be disabled as DEMOUSER is an existing database user.
    - Workspace Name : DEMOUSER

	![](./images/task1-6.png " ")

7. In the APEX Instance Administration page, click the **DEMOUSER** link in the success message.

    Note: This will log you out of APEX Administration so that you can log into your new workspace.
    ![](./images/task1-7.png " ")

8. On the APEX Workspace log in page, enter the **Password - WElcome123##** for the DEMOUSER workspace, check the Remember workspace and username checkbox, and then click **Sign In**.

    ![](./images/task1-8.png " ")

9. Click on **Set APEX Account Password**.

	![](./images/task1-9.png " ")

10. Navigate to the tab with Oracle cloud console, click on Profile icon, click on User Setting and Copy the email address.

	![](./images/task1-101.png " ")
    ![](./images/task1-102.png " ")

11. Navigate back to the APEX workspace, paste the email address in the Email Address field, provide the **Password - W3lcome123##** and confirm the password for the `demouser` and click on **Apply Changes**.

	![](./images/task1-111.png " ")
	![](./images/task1-112.png " ")

## Task 2: Define the REST End Points and Enable the Schema

By default the schema is not registered with ORDS. Let's define REST endpoints for the bank_ledger schema and enable the schema.

1. Click on the **SQL Workshop** drop-down menu.

	![](./images/task2-1.png " ")

2. Select **RESTful Services**.

	![](./images/task2-2.png " ")

3. Notice that the Schema Alias is `DEMOUSER`.

	![](./images/task2-31.png " ")

    Also, notice how clicking on modules shows that there are no modules defined.
    ![](./images/task2-32.png " ")

4. Click [here](https://objectstorage.us-ashburn-1.oraclecloud.com/p/nlUdle-us1TMJ-yTOL-sVxXazoDqQegCYakiXiA8zIONnFMWsaCt2u3tNHlz4sUx/n/c4u04/b/data-management-library-files/o/blockchain/ORDS-REST-Blockchain.sql) to download the ORDS-REST-Blockchain.sql file that has the SQL Script to REST Enable this schema and also to create modules for the bank_ledger table with the appropriate handlers.


5. Let's import the modules by clicking on **Import**.

    ![](./images/task2-5.png " ")

6. Click on **Choose File** and upload the ORDS REST Blockchain file that you just downloaded. Click on **Import**.

    ![](./images/task2-6.png " ")

7. Expand the **Modules** tab and notice that now you have the `blockchain` module created.

    ![](./images/task2-7.png " ")

8. Expand the blockchain module's tab to see the Templates - `rowdata` and `signdata`.

    ![](./images/task2-8.png " ")

9. Expand the `rowdata` tab, then select POST.

    ![](./images/task2-9.png " ")

10. Notice the sign PL/SQL procedure under Source field takes the seqId, instanceId, chainId as input parameters and give the row data as output response when you do a POST request.

    ![](./images/task2-10.png " ")

    View the rowdata PL/SQL procedure that takes the seqId, instanceId, chainId as input parameters and gives the row data as output response for the POST request:

    ```
    DECLARE
        row_data BLOB;
        buffer RAW(4000);
        inst_id BINARY_INTEGER;
        chain_id BINARY_INTEGER;
        sequence_no BINARY_INTEGER;
        row_len BINARY_INTEGER;
    BEGIN
        SELECT ORABCTAB_INST_ID$, ORABCTAB_CHAIN_ID$,
        ORABCTAB_SEQ_NUM$
        INTO inst_id, chain_id, sequence_no
        FROM BANK_LEDGER
        WHERE ORABCTAB_INST_ID$=:instanceId and
        ORABCTAB_CHAIN_ID$=:chainId and ORABCTAB_SEQ_NUM$=:seqId;
        DBMS_BLOCKCHAIN_TABLE.GET_BYTES_FOR_ROW_SIGNATURE('DEMOUSER','BANK_LEDGER',inst_id, chain_id, sequence_no, 1, row_data);
        row_len := DBMS_LOB.GETLENGTH(row_data);
        DBMS_LOB.READ(row_data, row_len, 1, buffer);
        :rowhex := RAWTOHEX(buffer);
        :status := 200;
    END;
    ```



11. After receiving the rowdata, the Node.js application which will install in the next lab will use that row data to do the signing using the other rest point -  POST method under the signdata.

12. Expand the signdata tab and select **POST**. Notice the sign PL/SQL procedure under Source field takes the cert_guid, chainId, instanceId, seqId as input parameters along with the rowdata to sign the row.

    ![](./images/task2-12.png " ")

    View the sign row PL/SQL procedure that takes the cert_guid,seqId, instanceId, chainId as input parameters and results the `status - 200` with a `message - Signature has been added to the row successfully.` if the row is signed successfully else it shows the `status - 400` with the `message - Error adding the signature to blockchain table.`

    ```
    BEGIN
        DBMS_BLOCKCHAIN_TABLE.SIGN_ROW('DEMOUSER','BANK_LEDGER', :instanceId
        , :chainId, :seqId, NULL, HEXTORAW(:signature), HEXTORAW(:cert_guid),
        DBMS_BLOCKCHAIN_TABLE.SIGN_ALGO_RSA_SHA2_256);
        :signature := :signature;
        :message := 'Signature has been added to the row successfully.';
        :status := 200;
    exception
    when others then
        :message := HEXTORAW(:signature)||'Error adding the signature to blockchain table.';
        :status := 400;
    END;
    ```



## Task 3: Import and Run the APEX Application

Now, we have the blockchain module, the handlers, and the templates defined. Let's import the apex application.

1. Click [here](https://objectstorage.us-ashburn-1.oraclecloud.com/p/iKksQSRJLdhamFt0fsQ4Fb0KhipXg-y5OP9hdFHqVH0CCLeSl_H9WV5HlBtujS8U/n/c4u04/b/data-management-library-files/o/blockchain/Blockchain-APEX-Application.sql) to download the Blockchain-APEX-Application.sql.

2.  Click on the **App Builder** drop-down menu and select **Import**.

    ![](./images/task3-2.png " ")

3. On the Import page, drag and drop or click on the **Drag and Drop** icon to upload the Blockchain-APEX-Application.sql file you just downloaded.

    Leave the default File Type - Database Application, page or Component Export and click **Next**.
    ![](./images/task3-3.png " ")

4. Click **Next**.

    ![](./images/task3-4.png " ")

5. In the Install Database Application page, notice that it will use the schema DEMOUSER, leave the defaults and click **Install Application**.

    ![](./images/task3-5.png " ")

6. Once the application is installed, click on **Run Application** to run the application.

    ![](./images/task3-6.png " ")

7. In the Blockchain APEX application sign in page, provide the **Username - DEMOUSER**, **Password - W3lcome123##** and click **Sign In**.

    ![](./images/task3-7.png " ")

## Task 4: Test the Blockchain Functionality in APEX Application

1. Click on **List of Transactions**.

    ![](./images/task4-1.png " ")

2. The List of Transactions page displays all the existing transactions from the database. The transactions with the empty shield symbol in the "Is Signed" column implies that the records are not signed. As the rows are not verified, it shows the message - `No Verification Process has been run!`.

    ![](./images/task4-2.png " ")

3. Click on **Create Transaction** button to create a new transaction. A Bank Ledger dialog box pops up.

    ![](./images/task4-3.png " ")

4. In the Bank Ledger dialog box, fill the values of your choice in the Bank, Deposit Date and Deposit Amount fields or type the values `Bank - 999`, `Deposit Date - 7/7/21` and `Deposit Amount - 1000` and click on **Insert Transaction** to create a new transaction.

    ![](./images/task4-4.png " ")

5. Notice that a new row is created with the details provided which is not signed and is shown on the List of Transactions.

    ![](./images/task4-5.png " ")

6. To update a record, click on the pencil icon of a row of your choice and update a value and click **Save Transaction**. It will throw an error.

    In this example, let's try to edit the record we just created. Change the "Bank" value to 998 and click **Save Transaction**. 

    ![](./images/task4-61.png " ")
    
    Note the error and click **Cancel**.

    ![](./images/task4-62.png " ")

7. To delete a record, click on the pencil icon of a row of your choice, click **Delete** and confirm the delete operation by clicking on **OK**. It throws an error - 

    In this example, let's try to delete the same record. Click on the pencil, click **Delete**, and then click **Ok**.

    ![](./images/task4-71.png " ")

    Note the error and click **Cancel**.

    ![](./images/task4-72.png " ")

8. Click on **Verify Rows** button and click **OK** on the dialog box to verify all the rows in the blockchain table. 

    ![](./images/task4-8.png " ")

9. After the verification is finished, notice that the *Verify Row Message* now shows the total number of rows and the number of records verified successfully. If the Total Rows and Verified Rows values are same means all the rows in table are verified successfully.

    ![](./images/task4-9.png " ")

10. Make note the Instance ID, Chain ID and Seq ID of a row that you want to sign. In the demo, we are going to sign the row with Instance ID - 1, Chain ID - 5, and Seq ID - 1.

   ![](./images/task4-10.png " ")
<!---
12. Replace the number 1 for the instanceId, chainId and seqId and update with your notes values in the below command.

    ```
    curl --location --request POST 'http://localhost:8080/transactions/row' --header 'Content-Type: application/json' --data '{"instanceId":1,"chainId":1,"seqId":1}'
    ```

    After replacing the instanceId, chainId and seqId values in the command, it should look like this:

    ```
    curl --location --request POST 'http://localhost:8080/transactions/row' --header 'Content-Type: application/json' --data '{"instanceId":1,"chainId":1,"seqId":1}'
    ```

13. Save this command in a noted to run which we will run in the next lab and sign the row.

--->

## Acknowledgements

* **Author** - Mark Rakhmilevich, Anoosha Pilli
* **Contributors** - Anoosha Pilli, Salim Hlayel, Product Manager, Oracle Database
* **Last Updated By/Date** - Brianna Ambler, August 2021
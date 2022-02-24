# Application Continuity

## Introduction

This lab walks you through the use and functioning of **acchk**, a database-resident untility for measuring the protection offered by Application Continuity. ACCHK provides guidance on the level of protection for each application that uses Application Continuity and helps guide you to increase protection, if required.

Estimated Lab Time: 20 Minutes

Watch the video below for an overview of the Application Continuity lab
[](youtube:KkwxbwII3O4)


### Prerequisites
- An Oracle LiveLabs or Paid Oracle Cloud account
- Lab: Generate SSH Key
- Lab: Build a DB System
- Lab: Services
- Lab: Install Sample Client
- Lab: Transparent Application Continuity


## Task 1:  Enabling ACCHK

The Application Continuity Protection Check (ACCHK) utility provides protection guidance for applications that use Application Continuity. For the protected applications, ACCHK also reports which operations of an application are protected, and which operations of an application are not protected.

1. Build the ACCHK views

**Note:** This step is only required for 19c, it is not required for later versions. If you are running on 19.11 or later 19cDBRU then you have to manually build the ACCHK views before using ACCHK. This is not required for versions later than 19c.

    Connect to the PDB, *pdb1* as the *system* user

    ````
    <copy>
    sudo su - oracle
    sqplus sys/W3lc0m3#W3lc0m3#@//<REPLACE SCAN NAME>/pdb1.<REPLACE DOMAIN NAME> as SYSDBA
    </copy>
    ````

Build the ACCHK views

    ````
    <copy>
    EXECUTE DBMS_APP_CONT_ADMIN.acchk_views;
    </copy>
    ````

    ![](./images/build_acchk_views.png " ")

2. Enable ACCHK

Still connected as the SYS user enable ACCHK

    ````
    <copy>
    execute dbms_app_cont_admin.acchk_set(true);
    </copy>
    ````

    ![](./images/enable_acchk.png " ")

3. Now run your application

   Make sure you are using a service that is enabled for Application Continuity (AC or TAC)

   There is no need to introduce any failures - just exercise as much of your application as possible

Use ACDEMO

    ````
    <copy>
    sudo su - oracle
    cd acdemo
    runtacreplay  
    </copy>
    ````
      ![](./images/runtacreplay.png " ")

or if using the Python client, first check the credentials being used
      ````
      sudo su - oracle
      cd /home/oracle/py_client
      vi db_config.py
      ````
the db_config.py file contains the credentials for logging in to the database. *user* is **hr**, password is the system password you have been using (default is W3lc0m3#W3lc0m3#) and the *dsn* can be either an EZConnect Plus syntax or a TNS alias, *minconn* is the number of connections to create (100). Note that while this client uses the OCI Session pool it does not really take advantage of its capabilities.

For example:

    ````
    $ more db_config.py
    user = "hr"
    pw = "W3lc0m3#W3lc0m3#"
    dsn = "//lvracdb-s01-2021-09-07-033110-scan.pub.racdblab.oraclevcn.com/tac_service.pub.racdblab.oraclevcn.com?CONNECT_TIMEOUT=120&TRANSPORT_CONNECT_TIMEOUT=2&RETRY_COUNT=20&RETRY_DELAY=3&LOAD_BALANCE=ON"
    minconn=100  
    ````

and then run the Python application

    ````
    <copy>
    cd /home/oracle/py_client
    python3 tac_demo.py
    </copy>
    ````    
4. Generate the acchk report from within SQL\*Plus

    ````
    <copy>
    set serveroutput on
    set lines 200
    EXECUTE dbms_app_cont_report.acchk_report(dbms_app_cont_report.FULL);
    </copy>
    ````

    ![](./images/acchk-report-2.png " ")

The report above shows the Python client *tac\_demo* using the TAC-enabled service *tac\_service*. As you can see this application is provided 100% protection from TAC.

5. You may have the choice of using AC or TAC (if you are using Oracle Database 19c clients and you use an Oracle Connection Pool). Our recommendation is to always start with TAC if you can, but there may be reasons why AC may be considered. Use acchk to compare the protection offered by Application Continuity

We will use the Python client in this example.
Edit *db\_config.py* to configure the *dsn* to use the ac-enabled service, *ac\_service*

    ````
    <copy>
    sudo su - oracle
    cd /home/oracle/py_client
    vi db_config.py
    </copy>
    ````

Select a service that is enabled for AC (ac\_service)

    ````
    user = "hr"
    pw = "W3lc0m3#W3lc0m3#"
    dsn = "//lvracdb-s01-2021-09-07-033110-scan.pub.racdblab.oraclevcn.com/ac_service.pub.racdblab.oraclevcn.com?CONNECT_TIMEOUT=120&TRANSPORT_CONNECT_TIMEOUT=2&RETRY_COUNT=20&RETRY_DELAY=3&LOAD_BALANCE=ON"

    ````

6. Ensure ACCHK is enabled
    ````
    <copy>
    sqplus sys/W3lc0m3#W3lc0m3#@//<REPLACE SCAN NAME>/pdb1.<REPLACE DOMAIN NAME> as SYSDBA
    </copy>
    ````
When in SQL\*Plus run the following command:

    ````
    execute dbms_app_cont_admin.acchk_set(true);
    ````    

and then run the Python application, tac\_demo

    ````

    ````    

The acchk report can be either **FULL** or **SUMMARY**. The report is produced per PDB per SERVICE
      ````
      <copy>
      sudo su - oracle
      cd py_client
      python3 tac_demo.py  
      </copy>
      ````  

7. And generate the report again

      ![](./images/acchk-report-3.png " ")

You should now see two entries, one for the service *tac\_service* and one for the service *ac\_service*.

The **failover_type** column indicates AUTO for TAC and TRANS (abbreviation of TRANSACTION) for AC. You may notice that the level of protection is quite different (for the python application at least).

This is an extreme example - there are 2 calls in each of the database requests, and one of them is not protected in the AC case, hence you only get approximately 50% protection. The reason it is not protected is shown:

    ````
    Event Type Error Code              Program               Module               Action        SQL_ID            Call      Total
    ---------------- ---------- -------------------- -------------------- -------------------- ------------- --------------- ----------
    NEVER_ENABLED      41463 python3@lvracdb-s01-                                                                                 99
    ````

8. ACCHK generates trace files from which it populates the database views DBA\_ACCHK\_EVENTS, DBA\_ACCHK\_EVENTS\_SUMMARY, DBA\_ACCHK\_STATISTICS and DBA\_ACCHK\_STATISTICS\_SUMMARY

Those views can be examined to gain more insight

ACCHK views can offer more information

       <<< Add something here >>>

9. Cleaning entries
If you are going to perform multiple tests with different applications using the same service you will have to remove the trace files ACCHK generates before changing clients. Not removing the trace files will produce an aggregated report (for all users of a given service) which may not be useful.

To remove the trace files used by ACCHK clean the entries under ORACLE BASE

In the LiveLabs systems ORACLE_BASE is set to /u01/app/oracle

    ````
    <copy>
    cd $ORACLE_BASE/diag/rdbms/<REPLACE DB NAME>/<REPLACE INSTANCE NAME>/trace
    rm <REPLACE INSTANCE NAME>_ora_*.trc
    </copy>
    ````
For example

    ````
    cd /u01/app/oracle/diag/rdbms/rackpemw_iad1pc/racKPEMW2/trace
    rm racKPEMW2_ora_*.trc
    ````

You may now *proceed to the next lab*.  

## Appendix Troubleshooting Tips

Currently no issues reported

## Acknowledgements
* **Authors** - Troy Anthony
* **Contributors** - Kay Malcolm
* **Last Updated By/Date** - Troy Anthony, September 2021

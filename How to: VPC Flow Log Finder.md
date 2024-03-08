# HOW TO: Use athena Queries and Python to Locate VPC traffic

1. **Create a Glue Database. Put a name of it that has not been used already**

  <div align="center" dir="auto">

![CreateGlueDatabase](https://github.com/lucabocha/CloudComputing/assets/44237986/af0883d4-c558-4cf1-aa4d-366996639d58)

  </div>

2. **Load the database in Athena**
     - Put the name of the DB previously created. Depending on the amount of DBs Athena might take a while to find the DB.
  
  <div align="center" dir="auto">
  
![AthenaLoadDB](https://github.com/lucabocha/CloudComputing/assets/44237986/47ca76ea-a060-4170-9e16-4bb3d4c43b87)

  </div>
  
3. **Create the table in Athena**
    - Open the query editor and paste the following block of sql code and then run the query:
    
          CREATE EXTERNAL TABLE IF NOT EXISTS <table name> ( 
            version int,
            account string,
            interfaceid string,
            sourceaddress string,
            destinationaddress string,
            sourceport int,
            destinationport int,
            protocol int,
            numpackets int,
            numbytes bigint,
            starttime int,
            endtime int,
            action string,
            logstatus string,
          ) 
          
          PARTITIONED BY (`date` date) 
          ROW FORMAT DELIMITED 
          FIELDS TERMINATED BY ' ' 
          LOCATION '<S3 URI where the flowlogs are stored>' 
          TBLPROPERTIES ("skip.header.line.count"="1");
      
      **Note:** the lines that are between the first parenthesis and the second parenthesis demark the columns that are going to be present on this table

Depdending on the information/fields you are logging in the flowlogs, you will have to add/remove columns in the sql query mentioned above 
  - For example, for this flow log:

        2 123456789010 eni-1235b8ca123456789 172.31.16.139 172.31.16.21 20641 22 6 20 4249 1418530010 1418530070 ACCEPT OK

  We can see it has 14 fields, each divided by an space

  2 
  123456789010 
  eni-1235b8ca123456789 
  172.31.16.139 
  172.31.16.21 
  20641 
  22 
  6 
  20 
  4249 
  1418530010 
  1418530070 
  ACCEPT 
  OK

  If we use the sql code mentioned above, each field will map to a different colum in the table as below: 

    2                         -----> version
    123456789010              -----> account
    eni-1235b8ca123456789     -----> interfaceid
    172.31.16.139             -----> sourceaddres
    172.31.16.21              -----> destinationaddress
    20641                     -----> sourceport
    22                        -----> destinationport
    6                         -----> protocol
    20                        -----> numpackets
    4249                      -----> numbytes
    1418530010                -----> starttime
    1418530070                -----> endtime
    ACCEPT                    -----> action
    OK                        -----> logstatus

  Now, if we had a flowlog like this one: 

    3 vpc-abcdefab012345678 subnet-aaaaaaaa012345678 i-01234567890123456 eni-1235b8ca123456789 123456789010 IPv4 10.0.0.62 52.213.180.42 5001 43418 10.0.0.62 52.213.180.42 6 63388 1219 1566848933 1566849113 ACCEPT 1 OK

  We would have to create a table using the following sql query: 

    CREATE EXTERNAL TABLE IF NOT EXISTS <table name> ( 
      version int,
      vpcid string,
      subnet string,
      ec2instance string,
      interfaceid string,
      account string,
      type string,
      sourceaddress string,
      destinationaddress string,
      sourceport int,
      destinationport int,
      pktsource string,
      pktdest string,
      protocol int,
      numbytes bigint,
      numpackets bigint,
      starttime int,
      endtime int,
      action string,
      tcpflags int,
      logstatus string,
    ) 
              
    PARTITIONED BY (`date` date) 
    ROW FORMAT DELIMITED 
    FIELDS TERMINATED BY ' ' 
    LOCATION '<S3 URI where the flowlogs are stored>' 
    TBLPROPERTIES ("skip.header.line.count"="1"); 

  So, at the end, each field will map to a table column in the following manner:

    3                            -----> version 
    vpc-abcdefab012345678        -----> vpcid
    subnet-aaaaaaaa012345678     -----> subnet
    i-01234567890123456          -----> ec2instance
    eni-1235b8ca123456789        -----> interfaceid
    123456789010                 -----> account
    IPv4                         -----> type
    10.0.0.62                    -----> sourceaddress
    52.213.180.42                -----> destinationaddress                              
    5001                         -----> sourceport
    43418                        -----> destinationport
    10.0.0.62                    -----> pktsource
    52.213.180.42                -----> pktdest
    6                            -----> protocol
    63388                        -----> numbytes
    1219                         -----> numpackets
    1566848933                   -----> starttime
    1566849113                   -----> endtime
    ACCEPT                       -----> action
    1                            -----> tcpflags
    OK                           -----> logstatus
  
Have in mind that there are more flowlog fields available that the ones mentioned on this article. You can refer to the following documentation that specifies the available fields for the flow logs:

[Logging IP traffic using VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html)

4. **Partition the table using dates**
    - For this you will have to run multiple sql lines to create the partitions

          ALTER TABLE <previously created table name> ADD
            PARTITION (`date`='2023-08-01') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/01'
            PARTITION (`date`='2023-08-02') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/02'
            PARTITION (`date`='2023-08-03') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/03'
            PARTITION (`date`='2023-08-04') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/04'
            PARTITION (`date`='2023-08-05') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/05'
            PARTITION (`date`='2023-08-06') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/06'
            PARTITION (`date`='2023-08-07') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/07'
            PARTITION (`date`='2023-08-08') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/08'
            PARTITION (`date`='2023-08-09') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/09'
            PARTITION (`date`='2023-08-10') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/10'
            PARTITION (`date`='2023-08-11') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/11'
            PARTITION (`date`='2023-08-12') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/12'
            PARTITION (`date`='2023-08-13') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/13'
            PARTITION (`date`='2023-08-14') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/14'
            PARTITION (`date`='2023-08-15') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/15'
            PARTITION (`date`='2023-08-16') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/16'
            PARTITION (`date`='2023-08-17') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/17'
            PARTITION (`date`='2023-08-18') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/18'
            PARTITION (`date`='2023-08-19') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/19'
            PARTITION (`date`='2023-08-20') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/20'
            PARTITION (`date`='2023-08-21') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/21'
            PARTITION (`date`='2023-08-22') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/22'
            PARTITION (`date`='2023-08-23') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/23'
            PARTITION (`date`='2023-08-24') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/24'
            PARTITION (`date`='2023-08-25') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/25'
            PARTITION (`date`='2023-08-26') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/26'
            PARTITION (`date`='2023-08-27') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/27'
            PARTITION (`date`='2023-08-28') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/28'
            PARTITION (`date`='2023-08-29') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/29'
            PARTITION (`date`='2023-08-30') LOCATION 's3://<bucket name>/<vpc-id>/AWSLogs/<account number>/vpcflowlogs/<region>/2023/08/30';

This will create one partition that refers to the flow logs for each of the days. This way you will be able to determine when the flow log happened

5. **Finally, run queries to match interesting traffic**
    - For this you use normal sql queries, here are some examples
      - Find traffic based on IP address (a sngle one)

            SELECT * FROM <table name> WHERE sourceaddress='10.192.80.5' 

      This will match all flow logs that have the 10.192.80.5 in the sourceaddress field. (You can review the available fields on the Athena query executed to create the table previously)

      - Find traffic based on the first two octects of the IP address

            SELECT * FROM <table name> WHERE regexp_like(sourceaddres, '^10\.192.*' 

      Find all the source addresses for traffic on all the flow logs. Except for the ones belonging to the local VPC (In this case its CIDR is: 10.192.0.0/16)

          SELECT DISTINCT "sourceaddress" FROM <table name> WHERE NOT regexp_like(sourceaddres, '^10\.192.*' 
  
      For this last example, you will be able to donwload the results in a file, which you can process via a script or program. [here](https://github.com/lucabocha/Programming-Scripting/blob/main/python/flowlog_private_cidr_finder.py) I have a script that will find all private IP ranges 

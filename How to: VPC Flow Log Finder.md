# HOW TO: Use athena Queries and Python to Locate VPC traffic

1. **Create a Glue Database. Put a name of it that has not been used already**

  <div align="center" dir="auto">

  ![GlueDatabaseCreation](https://github.com/lucabocha/CloudComputing/assets/44237986/0093c97f-34e3-4873-85c3-e9fb4514c3aa)

  </div>

2. **Load the database in Athena**
     - Put the name of the DB previously created. Depending on the amount of DBs Athena might take a while to find the DB.
  
  <div align="center" dir="auto">
  
  ![AthenaDBSearch](https://github.com/lucabocha/CloudComputing/assets/44237986/6f04e5a0-39c6-4792-8164-d1ac95eb3383)
  
  </div>
  
3. **Create the table in Athena**
    - Open the query editor and paste the following block of sql code:

The first option is: 
    
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
      vpcid string,
      subnetid string,
      instanceid string,
      tcpflags int,
      type string,
      pktsrcaddr string,
      pktdstaddr string,
      region string,
      azid string,
      sublocationtype string,
      sublocationid string,
      pktsrcawsservice string,
      pktdstawsservice string,
      flowdirection string,
      trafficpath string
    ) 
    
    PARTITIONED BY (`date` date) 
    ROW FORMAT DELIMITED 
    FIELDS TERMINATED BY ' ' 
    LOCATION '<S3 URI where the flowlogs are stored>' 
    TBLPROPERTIES ("skip.header.line.count"="1");

The second options is: 
    
    CREATE EXTERNAL TABLE IF NOT EXISTS <table name> ( 
      account-id int,
      action string,
      az-id string,
      bytes bigint,
      dstaddr string,
      dstport int,
      endtime bigint,
      flow-direction string,
      instance-id string,
      interface-id string,
      log-status string,
      packets int,
      pkt-dst-aws-service string,
      pkt-dstaddr string,
      pkt-src-aws-service string,  
      pkt-srcaddr string,
      protocol int, 
      region string, 
      srcaddr string, 
      srcport bigint, 
      start bigint, 
      sublocation-id string, 
      sublocation-type string, 
      subnet-id string, 
      tcp-flags int, 
      traffic-path string, 
      type string,
      version int,
      vpc-id string
    ) 

    PARTITIONED BY (`date` date) 
    ROW FORMAT DELIMITED 
    FIELDS TERMINATED BY ' ' 
    LOCATION '<S3 URI where the flowlogs are stored>' 
    TBLPROPERTIES ("skip.header.line.count"="1"); 

The difference between the two options is in the the order of the fields of on the flow logs, depending on this the order of the fields when creating the table will have to differ

You can refer to the following documentation that specifies the fields for the flow logs:

[Logging IP traffic using VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html)

4. **Partition the table using dates**
    - For this you will have to run multiple sql lines to create the partitions

          ALTER TABLE flowlogs_vpc_aaee05cd_august ADD
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
  
You will be able to donwload the results in a file, which you can process via a script or program. [here](https:test.com) I have created one that will find all 
  
  </ol>

  <p>In addition to this I have created the following script which allows me to sort through a  </p> [This is an external link to genome.gov](https://www.genome.gov/)
<br/>

</ol>

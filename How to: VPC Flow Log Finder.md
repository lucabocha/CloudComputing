<h1>HOW TO: Use athena Queries and Python to Locate VPC traffic</h1>  

<ol>
<li>Create a Glue Database. Put a name of it that has not been used already</li>
<br/>
<div align="center" dir="auto">

![GlueDatabaseCreation](https://github.com/lucabocha/CloudComputing/assets/44237986/0093c97f-34e3-4873-85c3-e9fb4514c3aa)

</div>

<li>Create a table using Athena</li>
  <ol>
    <li>Put the name of the DB previously created. Depending on the amount of DBs Athena might take a while to find the DB.</li>
    <br/>

<div align="center" dir="auto">
  
![AthenaDBSearch](https://github.com/lucabocha/CloudComputing/assets/44237986/6f04e5a0-39c6-4792-8164-d1ac95eb3383)

</div>
  <li>Open the query editor and paste the following block of sql code:</li>
  <p>
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
  </p>

  <p>
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
    
  </p>

  <p>The difference between the two options is in the the order of the fields of on the flow logs, depending on this the order of the fields when creating the table will have to differ</p>
  <p>You can refer to the following documentation that specifies the fields for the flow logs:</p>

[Logging IP traffic using VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html)

  </ol>

<br/>


</ol>



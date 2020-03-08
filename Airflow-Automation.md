# Airflow Automation

**********************************************************************************
Airflow Automation on Spark Standalone Cluster
**********************************************************************************

1. Build all Projects
   cd ~/workspace/FraudDetection
   ./deployInLocal.sh   

   cd ~/workspace/CreditcardProducer
   mvn package

   cd ~/workspace/Fraud-alert-dashboard
   mvn package

2. Start all the Servers
  #Start Cassandra Server 
    cassandra -f

  #Start the dashboard for visualization

    cd ~/workspace/Fraud-alert-dashboard/
    mvn exec:java -Dserver.port=8070 -Dexec.mainClass="com.datamantra.fraudalertdashboard.dashboard.FraudAlertDashboard"

  #Access Dashboard Web UI
    http://vm_ip:8070

  #Start Zookeeper Server
    cd /usr/local/kafka
    zookeeper-server-start etc/kafka/zookeeper.properties 

  #Start Kafka Server
    kafka-server-start etc/kafka/server.properties

	#Create topic
	kafka-topics --zookeeper localhost:2181 --create --topic creditcardTransaction  --replication-factor 1 --partitions 3


  #Spark Cluster Setup  
    #Start Spark Master
      start-master.sh

    #Start Spark Slave
      start-slave.sh spark://vg-vm:7077  

    #Access Spark Web UI
      http://vm_ip:8080/  


*******************************************************************************************************	  
3. Airflow Automation
*******************************************************************************************************  

  #Start Spark Import Job
    cd ~/workspace/FraudDetection
    ./manual/sparkImportToCassandraJob.sh 
  	

  #Create Airflow DAG directory
    mkdir ~/airflow/dags

  #Copy frauddetection.py file from FraudDetection Project to airflow dags directory 
    cp ~/workspace/FraudDetection/automate/airflow/frauddetection.py  ~/airflow/dags/.

  #Change the start time in frauddetection.py. Airflow uses UTC timezone  

  #Compile the changes
    python ~/airflow/dags/frauddetection.py

  #Reset the db so that airflow tables are created in airflow database
    airflow resetdb

	#Start Airflow Webserver
    airflow webserver --port 8090


  #Access Airflow Webserver
    http://localhost:8090

  #Start Airflow Scheduler  
    airflow scheduler

  #Wait for Spark Streaming Job to get scheduled through Apache Airflow, then start Kafka Producer
  #Start Producer 

    java -cp target/creditcard-tr-producer-1.0-SNAPSHOT-jar-with-dependencies.jar com.datamantra.producer.TrasactionProducer src/main/resources/application-local.conf

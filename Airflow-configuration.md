# Configure Airflow with Mysql

## Create new user and database in Mysql  
   1) Login to Mysql with root credentials  
      mysql -uroot -proot  
      
   2) CREATE USER 'hduser'@'localhost' IDENTIFIED BY 'hadoop123';  
      GRANT ALL PRIVILEGES ON * . * TO 'hduser'@'localhost';  
      FLUSH PRIVILEGES;  
      EXIT;  

  3) Login as hduser  
      mysql -uhduser -phadoop123  

   4) CREATE DATABASE airflow;  
    EXIT;  

## Change airflow.cfg file to use Mysql database
   cd ~/airflow
   vi airflow.cfg

  sql_alchemy_conn = mysql://hduser:hadoop123@localhost:3306/airflow
  load_examples = False   

## save and exit
## Rest Airflow Database
  airflow resetdb

## Start Airflow Webserver
  airflow webserver --port 8090

## Start Airflow Scheduler
  airflow scheduler

## Stop Webserver and scheduler by pressing CRTL-C in the respective terminal 

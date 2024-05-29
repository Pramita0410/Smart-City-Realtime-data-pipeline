# Real-Time Smart City Data Pipeline 

## Aim
Assuming there are vehicles commuting between Northeastern University, Boston to Times Square, New York and each vehicle is equipped with an IoT device that sends Vechicle data like accidents, location, Speed etc. The aim is to create an industry level seamless real-time data pipeline and to visualize this real-time data on dashboards with minimum latency. 
 
## Data
The data is simulated like an IoT device that generates data like
-  Vechicle Information: Speed, location, direction of the vehicle, vehicle model, etc
-  Gps Information: Timestamp, vehicle type, etc
-  Traffic Information: Camera snapshots, camera device, etc
-  Weather Information: Temperature, weather condition, precipitation, etc
-  Emergency Information: Incident type, Incident status, location, description, etc

## System Architecture

![smart-city-architecture](https://github.com/Pramita0410/Smart-City-Realtime-data-pipeline/assets/114774760/767597b5-f2f6-4436-a69b-71246993c15f)

- A python script fetches the data from the IoT device and this data is sent to the Kafka broker (This script acts like the producer). Different types of data is stored in different topics. Zookeeper sits on top of this Kafka broker.
- A spark achitecture with 1 Master node and 2 Worker nodes acts as the consumer to the Kafka broker. PySpark as a framkework was used to consume this data and push to a AWS S3 bucket.
- S3 bucket has below folder structure and stores each data in a parquet format

![image](https://github.com/Pramita0410/Smart-City-Realtime-data-pipeline/assets/114774760/dcdd010a-7b5c-4733-9f9a-e293692fa996)


- AWS Glue and Crawler is used to interpret the parquet file schema and store in 5 different tables in AWS database / Data Catalog

![image](https://github.com/Pramita0410/Smart-City-Realtime-data-pipeline/assets/114774760/63d60fe6-5ea3-4288-bacd-071937e65907)

- Ad hoc querying can be done on AWS Athena

![image](https://github.com/Pramita0410/Smart-City-Realtime-data-pipeline/assets/114774760/6c972f23-723b-467d-b6d6-df5249f077b9)

- A Redshift cluster is created to view this schema from data catalog and perform necessary transformations

![image](https://github.com/Pramita0410/Smart-City-Realtime-data-pipeline/assets/114774760/0d7f9b13-d400-43b8-83ff-420083d74a6d)

- Integrated AWS QuickSight with Redshift Cluster to create interactive dashboards and generate insights
![image](https://github.com/Pramita0410/Smart-City-Realtime-data-pipeline/assets/114774760/97fafff7-0d20-4a66-9d06-cedabf5a3a7a)


## How to run the project
- Clone this repository first
- Install all the python dependencies using `pip install -r requirements.txt` Python 3.8 was used at the time of development
- To start all the services: `sudo docker compose up -d` inside your project folder
- Run `sudo docker ps` to verify if all the services are running
- Run `python jobs/main.py` to run the producer simulation
- `sudo docker exec -it smart-city-spark-master-1 spark-submit --master spark://spark-master:7077 --packages org.apache.spark:spark-sql-kafka-0-10_2.12:3.5.0,org.apache.hadoop:hadoop-aws:3.3.1,com.amazonaws:aws-java-sdk:1.11.469 jobs/spark-city.py` to submit a job to master spark and consume the producer data



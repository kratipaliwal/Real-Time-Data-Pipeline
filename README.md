
# Real-Time Data Pipeline

This repository showcases an end-to-end real-time data pipeline built using Apache Kafka, Apache Spark, Apache Airflow, and Cassandra, all orchestrated with Docker. The pipeline handles data ingestion, processing, and storage in a scalable, distributed environment.

---

## 📦 Tech Stack

- **Docker** – Containerization of all services
- **Apache Kafka** – Distributed data streaming platform
- **Apache Zookeeper** – Kafka's coordination service
- **Apache Airflow** – Workflow orchestration for scheduling jobs
- **Apache Spark** – Real-time data processing from Kafka
- **Apache Cassandra** – NoSQL database for processed data
- **PostgreSQL** – Relational database used by Airflow
- **Schema Registry** – Schema management for Kafka topics
- **Kafka Control Center** – UI for monitoring Kafka clusters

---

## 🧱 Architecture Overview

1. **Data Ingestion**: Airflow triggers data producers that publish messages to Kafka topics.
2. **Data Streaming**: Kafka brokers stream the ingested data in real time.
3. **Data Processing**: Spark Streaming reads from Kafka and processes the data.
4. **Data Storage**: Processed data is written to Cassandra.
5. **Orchestration**: All services are containerized using Docker Compose.


## 📁 Project Structure

```
.
├── dags/
│   └── kafka_stream.py            # Airflow DAG for Kafka streaming
├── docker-compose.yml            # Docker Compose orchestration
├── requirements.txt              # Python dependency file
├── script/
│   └── entrypoint.sh             # Initialization script for containers
└── spark_stream.py               # Spark job for processing Kafka messages
```

---

## ⚙️ Setup Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/realtime-data-pipeline.git
cd realtime-data-pipeline
```

### 2. Install Python Dependencies

```bash
pip install -r requirements.txt
```

### 3. Make Entrypoint Script Executable

```bash
chmod +x script/entrypoint.sh
```

### 4. Start Docker Containers

```bash
docker compose up -d
```

> ⚠️ Ensure the `AIRFLOW__WEBSERVER__SECRET_KEY` value is consistent between the Airflow webserver and scheduler services in `docker-compose.yml`.

---

## 🛰 Kafka Setup

- Kafka and Zookeeper are launched as part of the Docker Compose stack.
- Kafka topic: `users_created`
- Use the Kafka Control Center at [http://localhost:9021](http://localhost:9021) to inspect topics and messages.

---

## 🌐 Airflow DAG Execution

1. Visit the Airflow UI: [http://localhost:8080](http://localhost:8080)
2. Enable and trigger the `user_automation` DAG.
3. This will publish messages to the Kafka topic.

---

## 🔄 Spark Streaming to Cassandra

To start the Spark job that reads from Kafka and writes to Cassandra:

```bash
spark-submit --master spark://localhost:7077 spark_stream.py
```

- Spark Master UI: [http://localhost:9090](http://localhost:9090)
- Ensure Spark Worker is in `ALIVE` state.

---

## 🗃️ Cassandra Table Validation

Access Cassandra shell:

```bash
docker exec -it cassandra cqlsh -u cassandra -p cassandra localhost 9042
```

Create keyspace and table (automated via Spark job):

```sql
DESCRIBE spark_streams.created_users;
SELECT * FROM spark_streams.created_users;
```

---

## ✅ Verifying the Pipeline

Once the pipeline is running:
- Kafka receives and streams `users_created` events.
- Spark processes these events and writes them to Cassandra.
- You can query Cassandra to verify the data is persisted.

---

## 🧪 Example Output

```sql
SELECT * FROM spark_streams.created_users;

 id                                   | address        | email               | first_name | gender | last_name | phone       | picture | post_code | registered_date | username
-------------------------------------+----------------+---------------------+------------+--------+-----------+-------------+---------+-----------+-----------------+----------
 71a8cf2a-...                        | 123 Main St    | alice@example.com   | Alice      | female | Smith     | 123-456-7890| img.jpg | 90210     | 2023-10-01      | alices
```

---

## 📌 Notes

- The Airflow scheduler and webserver use PostgreSQL as the backend.
- Spark reads the Kafka topic `users_created` and processes it using the schema defined in `spark_stream.py`.
- All components are containerized for consistency and scalability.

---

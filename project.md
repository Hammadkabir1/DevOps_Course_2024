# Project


### Monitor and Visualize Logs Using ELK Stack

- **Objective**: Set up a centralized logging and monitoring system for a microservices application using the ELK Stack (Elasticsearch, Logstash, and Kibana).

---

**Steps Taken:**

### 1. Configure Logstash for Log Aggregation:

- Created a `logstash.conf` file to aggregate logs from multiple microservices:

#### Code:
```conf
input {
    file {
        path => "/var/log/*.log"
        start_position => "beginning"
    }
}

filter {
    grok {
        match => { "message" => "%{TIMESTAMP_ISO8601:timestamp} %{LOGLEVEL:loglevel} %{GREEDYDATA:message}" }
    }
}

output {
    elasticsearch {
        hosts => ["http://elasticsearch:9200"]
        index => "logs-%{+YYYY.MM.dd}"
    }
}
```

---

### 2. Set Up Elasticsearch for Storing Logs:
- Launched an Elasticsearch container to store and index the logs.

#### Code:
```yaml
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.17.0
    environment:
      - discovery.type=single-node
    ports:
      - "9200:9200"
```

---

### 3. Deploy Kibana for Log Visualization:
- Added a Kibana container to visualize the aggregated logs.

#### Code:
```yaml
services:
  kibana:
    image: docker.elastic.co/kibana/kibana:7.17.0
    ports:
      - "5601:5601"
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
```

---

### 4. Deploy the Full Stack Using Docker Compose:
- Created a `docker-compose.yml` file to deploy the ELK stack and microservices.

#### Code:
```yaml
version: '3'
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.17.0
    environment:
      - discovery.type=single-node
    ports:
      - "9200:9200"

  logstash:
    image: docker.elastic.co/logstash/logstash:7.17.0
    volumes:
      - ./logstash.conf:/usr/share/logstash/pipeline/logstash.conf
    ports:
      - "5000:5000"

  kibana:
    image: docker.elastic.co/kibana/kibana:7.17.0
    ports:
      - "5601:5601"
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
```

---

### 5. Test the Setup:
- Launched the entire stack using Docker Compose:
```bash
docker-compose up --build
```

- Sent sample logs to Logstash for testing:
```bash
echo "2024-11-25 12:30:45 INFO Service started successfully" >> /var/log/test-service.log
```

- Verified the logs were indexed in Elasticsearch and visualized in Kibana.

---

### Outcome:
- Implemented a centralized logging system using the ELK stack.
- Logs from multiple microservices were aggregated, stored, and indexed in Elasticsearch.
- Visualized the logs in Kibana with filters, graphs, and dashboards for efficient monitoring and troubleshooting.

---

### Conclusion:
By centralizing logs using the ELK stack, this project simplifies monitoring and debugging in a microservices architecture. It enhances observability, reduces debugging time, and ensures smooth operations for the application.

---

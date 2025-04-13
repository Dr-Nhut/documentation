# Part 1: Introduce
## What is ElasticSearch?
- ElasticSearch is an analytics and full-text search engine, query and analyze structured data as an analytic platform, anomality detection, forecast future values with machine learning

## How does ElasticSearch work?
- Data is stored as Documents
- A document's data is separated into Fields
- Written in Java, built on Apache Lucene
- Easy to use, and highly scalable

## Overview Elastic Stack

### Kibana
- Kibana is an analytics and visualization platform
- Use many built-in visualizations: pie charts, line charts,...
- Set up machine learning for detection and forecasting 
- Provide an interface to manage parts of ElasticSearch and Logstash, such as authentication and authorization

### Logstash
- Is a data processing pipeline (event), could be log file entries, ecommerce orders, customers, chat messages,...
- Logstash pipeline consists of three parts - or stages: input, filters (process), output
- Logstash is horizontally scalable

### X-pack
- X-pack is a pack of futures that adds additional functionality to Elasticsearch and Kibana

#### Security
- X-pack adds both authentication and authorization to both Elasticsearch and Kibana

#### Monitoring
- Gain insight into how the Elastic Stack is running such as can see CPU and memory usage, disk space,... and get notified if something goes wrong

#### Alerting
- Be notified by e-mail, Stack, or others when something goes wrong

#### Reporting 
- Export the Kibana visualizations and dashboards to PDF files, data to CSV files
- You can generate reports on demand, or schedule them and then receive them directly within your e-mail inbox
- Customize the look and fell of reports

#### Machine Learning 
- Enable machine learning for ElasticSearch and Kibana

#### Graph
- Analyze the relationship in your data such as show related products or suggest songs
- Integrate into applications with an API
- Visualize the relationships within Kibana 

#### ElasticsearchSQL
- Query ElasticSearch with SQL
- ElasticSearch queries are written in Query DSL
- Send SQL over HTTP or through a JDBC driver

### Beats
- Beats is a collection of data shippers
- Example: FileBeats is used for collecting log files and sending tne log entries off to either Logstash or Elasticsearch
- Another beats: metricbeats, packetbeat, winlogbeat, auditbeat, heartbeat

## Architecture

# Part 2: Getting Started
## ElasticSearch vs OpenSearch
- Core API are the same
- The differences are most apparent in regards to the vertical solutions such as observability
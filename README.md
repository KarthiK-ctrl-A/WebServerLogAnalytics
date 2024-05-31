
# Web Server Log Analytics

Web server log analytics involves the collection, processing, and visualization of log data to monitor and enhance the performance, security, and usage patterns of web servers. Our approach begins with fetching logs from Apache web servers, which are then monitored and shipped using Filebeat. The log data is streamed in real-time via Apache Kafka and processed with Faust Stream to transform it into the required format. This processed data is stored in Apache Pinot, a real-time OLAP datastore, and visualized using Apache Superset. This comprehensive pipeline ensures efficient handling and insightful analysis of web server logs, facilitating improved system management and optimization.


## Deployment

To deploy this project run


-- The following generates the Logs for testing purpose

```bash
  python3 -m Data/generateLogs.py -o LOG
```

-- as per our configuration, file beats has been configured in such a way that, whenever the new logs are generated, it will be posted on event stream platform 'Kafka'

-- Now run , the faust stream process to masssage the data

```bash
  python3 -m app.py
```

-- The above process will store the processed data into the same kafka and now data is ready to be stored in OLAP platform

-- Our ecosystem is completely on docker, so execute the following commands to trigger the storage from our kafka topic to pinot databse

```bash
  docker run -v $PWD/pinot/config:/config \
 --network analysing-log-files_default \
 apachepinot/pinot:0.11.0 \
 AddTable \
    -schemaFile /config/schema.json \
    -tableConfigFile /config/table.json \
    -controllerHost pinot-controller \
    -exec
```

-- Our data is now ready to use , we ca check this my querying our table at port 9000

-- Now similarly execute the following command to run the apache superset

```bash
  docker exec -it superset superset fab 
  create-admin \ 
        --username admin \
        --firstname Superset \
        --lastname Admin \ 
        --email admin@superset.com \ 
        --password admin
```
```bash
  docker exec -it superset superset db upgrade
  docker exec -it superset superset init
```

-- Ah! Now we can use the table from pinot as a data source here in superset and visualize our data here...
## Screenshots

![image](https://github.com/KarthiK-ctrl-A/WebServerLogAnalytics/assets/64576142/14255d08-fab1-498d-9c3d-c00f0e8a7842)

![image](https://github.com/KarthiK-ctrl-A/WebServerLogAnalytics/assets/64576142/4d779de3-85d8-4c28-bd34-693321b0012d)

![Screenshot (697)](https://github.com/KarthiK-ctrl-A/WebServerLogAnalytics/assets/64576142/37c23983-4e3b-426b-b3fa-1774a841c890)






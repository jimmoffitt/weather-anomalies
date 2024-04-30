The anomaly detection algorithms are based on this schema: 

```sql
SELECT id, 
       timestamp, 
       value
```

The weather monitoring project uses the following schema. Actually, there are a total of seven different weather measurements. In addition to temperature, the data includes wind speed and direction, humidity, cloud cover, precipitation, and barometric pressure. Here the focus is on temperature, and these algorithms will be used to detect temperature anomalies. If you wanted to detect anomalies in other weather parameters, you could replace `temp_f` with another type.  

```sql
SELECT site_name, 
       timestamp, 
       temp_f
```

### IQR examples

```sql
SELECT id,
       quantileExact(0.25) (value) AS lower_quartile,
       quantileExact(0.75) (value) AS upper_quartile

```

```sql
SELECT site_name,
       quantileExact(0.25) (temp_f) AS lower_quartile,
       quantileExact(0.75) (temp_f) AS upper_quartile
```

```sql
SELECT DISTINCT timestamp, 
       id, 
       value
```

```sql
SELECT DISTINCT timestamp, 
       site_name, 
       temp_f
```

### Rate-of-change example

```sql
lagInFrame(timestamp, 1) OVER 
        (PARTITION BY id ORDER BY timestamp ASC ROWS BETWEEN 1 PRECEDING AND 1 PRECEDING) AS previous_timestamp, 
lagInFrame(temp_f, 1) OVER
        (PARTITION BY id ORDER BY timestamp ASC ROWS BETWEEN 1 PRECEDING AND 1 PRECEDING) AS previous_value
```

```sql
lagInFrame(timestamp, 1) OVER 
        (PARTITION BY site_name ORDER BY timestamp ASC ROWS BETWEEN 1 PRECEDING AND 1 PRECEDING) AS previous_timestamp, 
lagInFrame(temp_f, 1) OVER
        (PARTITION BY site_name ORDER BY timestamp ASC ROWS BETWEEN 1 PRECEDING AND 1 PRECEDING) AS previous_value
```

### Z-score example

```sql
 WITH stats AS (
      SELECT id,
             avg(value) AS average,
             stddevPop(value) AS stddev
        FROM incoming_data
        WHERE timestamp BETWEEN (NOW() - INTERVAL stats_window_minutes MINUTE) AND NOW()
           {% if defined(sensor) %}               
              AND id = {{ String(sensor, description="Used to select a single sensor of interest. Optional.")}}       
           {% end %}  
        GROUP BY id  
    )

```

```sql
 WITH stats AS (
      SELECT site_name,
             avg(temp_f) AS average,
             stddevPop(temp_f) AS stddev
        FROM weather_data.weather_data
        WHERE timestamp BETWEEN (NOW() - INTERVAL stats_window_minutes MINUTE) AND NOW()
           {% if defined(city) %}               
              AND site_name = {{ String(city, description="Used to select a single city of interest. Optional.")}}       
           {% end %}  
        GROUP BY site_name  
    )
```



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

To update the example algorithms, you can just replace `id` with `site_name`, and `value` with `temp_f`. 

One key difference is that the example schema uses a numeric sensor id, whereas the weather data schema uses a string (a city name). When setting these up as query parameters, the data type the parameter is cast to needs to be updated from `Int32` to `String`:

```sql
{% if defined(sensor) %}               
    AND id = {{ Int32(sensor, description="Used to select a single sensor of interest. Optional.")}}       
{% end %}  
```

```sql
{% if defined(city) %}               
   AND site_name = {{ String(city, description="Used to select a single city of interest. Optional.")}}       
{% end %}  
```



### Highest requested host

```
=>SELECT host, COUNT(host) AS no_of_hosts FROM ngnix_access_log GROUP BY host ORDER BY COUNT(host) DESC LIMIT 1;

=> host column.
=> COUNT() => function is used to count the hosts entries.
=>GROUP BY => puts all the hosts into a single module/group
=> LIMIT => Clause used to mention LIMIT
=> DESC => descending order 

=>+----------------------+-----------+
| host                 |no_of_hosts |
+----------------------+-----------+
| prod.ppops.pm5       |     11091 |
+----------------------+-----------+

```

### Highest requested upstream_ip

```
SELECT upstream_ip_port, COUNT(upstream_ip_port) AS no_of_ip FROM ngnix_access_log GROUP BY upstream_ip_port ORDER BY COUNT(upstream_ip_port) DESC LIMIT 1;

=>+----------------------+-----------+
|  upstream_ip_port    |no_of_ip    |
+----------------------+-----------+
|  10.77.22.10:80     |     1701    |
+----------------------+-----------+
```
### Highest requested path

```
SELECT path, COUNT(path) AS path FROM ngnix_access_log GROUP BY path ORDER BY COUNT(path) DESC LIMIT 1;

+-------------------------------------+-----------+
| path                                | FREQUENCY |
+-------------------------------------+-----------+
| /recharge/phone/bandwidth-monitored |        47 |
+-------------------------------------+-----------+ 
```

### Total requests per status code (Ex: count of requests returning 404/401/502/504/500/200)

```
SELECT statusCode, COUNT(statusCode) AS number FROM ngnix_access_log WHERE statusCode LIKE '404'; 
SELECT statusCode, COUNT(statusCode) AS number FROM ngnix_access_log WHERE statusCode LIKE '401';
SELECT statusCode, COUNT(statusCode) AS number FROM ngnix_access_log WHERE statusCode LIKE '502';
SELECT statusCode, COUNT(statusCode) AS number FROM ngnix_access_log WHERE statusCode LIKE '504';
SELECT statusCode, COUNT(statusCode) AS number FROM ngnix_access_log WHERE statusCode LIKE '500';
SELECT statusCode, COUNT(statusCode) AS number FROM ngnix_access_log WHERE statusCode LIKE '200';

all the can be added inside UNION ALL where all the statements can be exectuted at once

+------------+-------+
| statusCode | number|
+------------+-------+
| 404        |   522 |
| 401        |   419 |
| 502        |   641 |
| 504        |   414 |
| 500        |   521 |
| 200        | 43826 |
+------------+-------+ 
```

### Top 5 requests by upstream_ip

```
SELECT upstream_ip_port, COUNT(upstream_ip_port) AS number FROM ngnix_access_log GROUP BY upstream_ip_port ORDER BY COUNT(upstream_ip_port) DESC LIMIT 5;
+------------------+---------------+
| upstream_ip_port | number        |
+------------------+---------------+
| 10.77.22.10:80   |          1701 |
| 10.77.27.14:8443 |          1683 |
| 10.77.22.11:443  |          1675 |
| 10.77.22.12:443  |          1603 |
| 10.77.22.11:80   |          1598 |
+------------------+---------------+ 

```

### Top 5 requests by host

```
SELECT host, COUNT(host) AS number FROM ngnix_access_log GROUP BY host ORDER BY COUNT(host) DESC LIMIT 5;

+----------------------+---------------+
| host                 | number        |
+----------------------+---------------+
| prod.ppops.pm5       |         11091 |
| appone.ppops.com     |         11062 |
| api.ppops.com        |         10815 |
| apptwo.ppops.com     |         10713 |
| apptwo-new.ppops.com |         10591 |
+----------------------+---------------+  

```
### Top 5 requests by bodyBytesSent

```
SELECT bodyBytesSent, COUNT(bodyBytesSent) AS number FROM ngnix_access_log GROUP BY bodyBytesSent ORDER BY COUNT(bodyBytesSent) DESC LIMIT 5;

+---------------+---------------+
| bodyBytesSent | number        |
+---------------+---------------+
| 38            |           179 |
| 70            |           160 |
| 51            |           158 |
| 61            |           157 |
| 60            |           157 |
+---------------+---------------+
```

### Top 5 requests by path

```
SELECT path, COUNT(path) AS number FROM ngnix_access_log GROUP BY path ORDER BY COUNT(path) DESC LIMIT 5;

+-------------------------------------+---------------+
| path                                | number        |
+-------------------------------------+---------------+
| /recharge/phone/bandwidth-monitored |            47 |
| /recharge/dth/empowering            |            45 |
| /myapi/kyc/needs-based              |            38 |
| /recharge/dth/info-mediaries        |            37 |
| /myapi/kyc/groupware                |            35 |
+-------------------------------------+---------------+ 
```

### Top 5 requests with the highest response time

```
SELECT host,ip,responseTime FROM ngnix_access_log GROUP BY responseTime ORDER BY responseTime DESC LIMIT 5;

----------------------+-----------------+--------------+
| host                 | ip              | responseTime |
+----------------------+-----------------+--------------+
| prod.ppops.pm5       | 132.175.122.124 | 9.999        |
| appone.ppops.com     | 51.165.1.241    | 9.995        |
| apptwo-new.ppops.com | 56.23.55.180    | 9.975        |
| appone.ppops.com     | 176.208.99.251  | 9.966        |
| api.ppops.com        | 79.220.246.255  | 9.960        |
+----------------------+-----------------+--------------+ 
```

### Get all the requests taking more than 2/5/10 secs to respond.

```
  SELECT responseTime,ip,host FROM ngnix_access_log WHERE responseTime>=2;
  SELECT responseTime,ip,host FROM ngnix_access_log WHERE responseTime>=5;
  SELECT responseTime,ip,host FROM ngnix_access_log WHERE responseTime>=10;

```

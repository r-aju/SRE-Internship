# SHELL SCRIPTING 

```
A log file was given where the details of the format included as-

ip, time, httpMethod, path, httpVersion, statusCode, responseTime, upstream_ip:port, bodyBytesSent, referrer, userAgent, ssl_protocol, content_type, host
where we extract the data and run scripts based on the given conditions.  
```
LogFile = `https://raw.githubusercontent.com/aratik711/nginx-log-generator/main/access.log (Link)`

```
* Use wget command to download the logfile
  wget = is derived for world wide web and supports downloading downloading via HTTP,HTTPS and FTP protocols
  wget https://raw.githubusercontent.com/aratik711/nginx-log-generator/main/access.log
```

>  Executing the shell scripts
```
     Some of the links which I reffered while executing the shell scripts are as follows
     https://www.youtube.com/watch?v=cQepf9fY6cE&list=PLS1QulWo1RIYmaxcEqw5JhK3b-6rgdWO_&index=1   (Revised basic of Shell Scripts)
     https://www.youtube.com/watch?v=TzWZLcF2yMU&list=PLNlxfQ7JYooHd_Ptnzxlc8JWdTczNpUR0&index=8   (Revised Grep, Sed, and awk)
     https://www.gnu.org/software/gawk/manual/gawk.html#Splitting-By-Content                        (awk basics)
```

> question1 (highest requested host) 
```
  => echo "highest requested Host"
  * awk -vFPAT='([^" "]*)|("[^"]+")' -vOFS=" " '{if(count[$15]++ >= max) max = count[$15]} END {for ( i in count ) if(max == count[i]) print i, count[i] }' access.log
  * -vFPAT => is used to give desired spacing between columns.
  * -vOFS => is used to format output file system spacing.  
 * Variables used are max , count function, $15 refers to 15th column (Host column)
         (Output =>676 apptwo.ppops.com)
```
>  (Output =>676 apptwo.ppops.com)

> question2 (highest requested upstream_ip)
```
echo "highest requested upstream_ip"
awk -vFPAT='([^" "]*)|("[^"]+")' -vOFS=" " '{if(count[$9]++ >= max) max = count[$9]} END {for ( i in count ) if(max == count[i]) print i, count[i] }' access.log
$15 refers to 9th column (upstream_ip)
```
> (Output => 117 10.77.22.10:80)

> question3 (highest requested path (upto 2 subdirectories ex: /check/balance)
```
echo "highest requested path"
awk -vFPAT='([^" "]*)|("[^"]+")' -vOFS=" " '{if(count[$5]++ >= max) max = count[$5]} END {for ( i in count ) if(max == count[i]) print i, count[i] }' access.log
$5 refers to 5th field/column (highest requested path) 
(Output => 6 /myapi/consumers/open%20system)  
```
> question4 (total requests per status code (Ex: count of requests returning 404/401/502/504/500/200)
```
      echo "Total requests per status code"
      awk '{print $7}' access.log | sort |uniq -c
      sort => is used to sort the particular column
      uniq -c => prints only the unique elements rather than repeating elements.
      ( Output => 464 301
                  500 201
                  501 501
                  510 304
                  519 403
                  519 503
                  521 406
                  521 500
                  522 400
                  522 404
                  533 416
                  548 100
                  570 205
                  571 203
                  585 302
                  626 204
                  641 502
                  43826 200)
```

> question5 (Top requests =>top 5 requests by upstream_ip)
```
echo "Top 5 requests by upstream_ip"
awk -vFPAT='([^" "]*)|("[^"]+")' -vOFS=" " '{count[$9]++} END {for ( i in count ) print i, count[i] }' access.log | sort -rn | head -5
$9 => upstream_ip field. 
sort -k2 -r => Sorts and considers everything available in the column.
       =>head -5 -> is used to print top 5 .
        (Output => 1701 10.77.22.10:80
                  1683 10.77.27.14:8443
                  1675 10.77.22.11:443
                  1603 10.77.22.12:443
                  1598 10.77.22.11:80) 
```
> question6 (Top requests =>top 5 requests by host)-
```
echo "Top 5 requests by host"
awk -vFPAT='([^" "]*)|("[^"]+")' -vOFS=" " '{count[$15]++} END {for ( i in count ) print i, count[i] }' access.log | sort -rn | head -5       
$15 => host requested field.
Output => (11091 prod.ppops.pm5
           11062 appone.ppops.com
           10815 api.ppops.com
           10713 apptwo.ppops.com
           10591 apptwo-new.ppops.com)
```

> question7 (Top requests =>top 5 requests by bodyBytesSent)
```
echo "Top 5 requests by bodyBytesSent"
awk -vFPAT='([^" "]*)|("[^"]+")' -vOFS=" " '{count[$10]++} END {for ( i in count ) print i, count[i] }' access.log | sort -rn | head -5  
$10 => bodyBytesSent field.
 Output => (179 38
            160 70
            158 51
            157 61
            157 60)
```
> question8 (Top requests =>top 5 requests by path (upto 2 subdirectories ex: /check/balance)

```
   echo "top 5 requests by path "
   awk -vFPAT='([^" "]*)|("[^"]+")' -vOFS=" " '{count[$5]++} END {for ( i in count ) print i, count[i] }' access.log | sort -rn | head -5  
    $5 => requested by path field.
     Output => (47 /recharge/phone/bandwidth-monitored
                        45 /recharge/dth/empowering
                        38 /myapi/kyc/needs-based
                        37 /recharge/dth/info-mediaries
                        35 /myapi/kyc/groupware)
```

> question9 (Top requests =>top 5 requests with the highest response time)
```
 echo "top 5 requests by highest response time"
 awk -vFPAT='([^" "]*)|("[^"]+")' -vOFS=" " '{count[$8]++} END {for ( i in count ) print i, count[i] }' access.log | sort -rn -r | head -5                                   
 $8 => response time field
         => Output => ( 101 0.814
                        96 0.101
                        91 0.238
                        91 0.051
                        90 0.733)
```

> question10 (Top requests =>get top 5 requests returning 200/5xx/4xx per host)

```
echo "top 5 requests returning"
awk -vFPAT='([^" "]*)|("[^"]+")' -vOFS=" " '{count[$7]++} END {for ( i in count ) print i, count[i] }' access.log | sort -rn | head -5 
$7 => returning requests field
         => Output => ( 414 504
                        419 401
                        440 405
                        464 301
                        500 201)
```
> question11 (find the time last time a particular staus code was received for a particular host)

```
 echo "last time when particular status requested from the host"
  awk '{ print $15 " " $7 }' access.log | sort | uniq     
   $15 => host field.
    $7 =>  status code.
     Output => (7170 Mac 200
                     3715 Intel 200
                     3705 PPC 200
                     3075 Windows 200
                     3022 i686; 200
                     2910 x86_64) 200
                     2855 x86_64; 200
                     2719 i686) 200
                     1517 iPhone 200
                     1410 OS 200
                     1009 Win 200
                     710 en-US; 200
                     657 en-US) 200
                     613 6.1) 200
                     598 5.0) 200
                     567 6.2; 200)
```

> question12 (get all request for the last 10 minutes)

```
echo "get all request for the last 10 minutes"
awk '/^$(date --date="-10 min" "+%b %_d %H:%M")/{time++} time"\ | sort | uniq -c
To get the format reffered => https://unix.stackexchange.com/questions/593108/nginx-print-log-for-last-10min-where-nginx-has-time-stamp-format-time-iso8601
```


> question13 (get all the requests taking more than 2/5/10 secs to respond)
```

=>echo"The requests taking more than 2/5/10 secs to respond"
   => awk '{if ( $8 >= 10 )print $8 " -> " $9 }' access.log | sort | uniq | sort  -rn          (greater than 10 sec)
   => awk '{if ( $8 >= 5 && $8 <10 )print $8 " -> " $9 }' access.log | sort | uniq | sort  -rn (greater than  5 sec) 
   => awk '{if ( $8 >= 2 && $8 < 5 )print $8 " -> " $9 }' access.log | sort | uniq | sort  -rn (greater than 2 sec)

   => Output => 
   1 10.006 229.218.34.118 apptwo.ppops.com
   1 10.031 121.153.105.80 api.ppops.com
   1 10.045 172.83.218.111 appone.ppops.com            (more than 10 seconds)
   1 10.069 146.32.3.148 prod.ppops.pm5
   1 10.113 219.63.76.170 appone.ppops.com

  
   1 10.006 229.218.34.118 apptwo.ppops.com
   1 10.031 121.153.105.80 api.ppops.com
   1 10.045 172.83.218.111 appone.ppops.com          (more than 5 seconds)
   1 10.069 146.32.3.148 prod.ppops.pm5
   1 10.113 219.63.76.170 appone.ppops.com

   
   1 10.006 229.218.34.118 apptwo.ppops.com
   1 10.031 121.153.105.80 api.ppops.com
   1 10.045 172.83.218.111 appone.ppops.com          (more than 2 seconds)
   1 10.069 146.32.3.148 prod.ppops.pm5
   1 10.113 219.63.76.170 appone.ppops.com
   1 10.113 219.63.76.170 appone.ppops.com
```

> Key Points On AWK and SHELL

```
=> Awk is a complete programming language used for processing input.
=> Awk has some varaibles which are dynamically set by itself (ex=> NF,NR,RS,FS ..)
=> Shell Scripts => Interprets user commands which are directly written by the user from a file called as shell script.
=>Shell Scripts are interpreted and are not compiled.
```



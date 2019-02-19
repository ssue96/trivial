## AWS ec2 instance에 mysql 설치하는 방법  
**참고**  
https://www.lesstif.com/pages/viewpage.action?pageId=24445108  
1. mysql install  
```
$ sudo wget https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm   
$ sudo yum localinstall mysql57-community-release-el7-11.noarch.rpm   
$ sudo yum install mysql-community-server  
$ systemctl start mysqld.service 
``` 
        
2. mysql 초기 비밀번호 찾기  
```
$grep 'password' /var/log/mysqld.log  
```
        
3. 암호 변경  
```
mysql>set password=password('qwert123');  
mysql>flush privileges;  
```  

4. mysql 서비스 시작
```
$ service mysql start  
$ service mysql stop  
$ service mysql restart  
$ service mysql status  
```

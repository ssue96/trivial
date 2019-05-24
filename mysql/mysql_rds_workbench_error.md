# mysql workbench에서 AWS RDS 연결 에러날 때  

RDS 인스턴스 생성 후 워크벤치에서 연결하려고 했는데 에러가 났다. 이 때 할 일  
1. publicly accessible에 yes로 체크
2. security group inbound 체크 - 소스 부분을 my ip로 해라  

**참조**  
https://github.com/designdevelop/rdb-study/wiki/AWS-RDS-%EC%A0%91%EC%86%8D%EA%B6%8C%ED%95%9C-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0  
https://aws.amazon.com/ko/premiumsupport/knowledge-center/connect-rds-mysql-workbench/  

일단 문제가 생기면 security group문제라고 생각하면 된다. 
마지막 수단으로는.. 컴퓨터를 껐다 킨다..   


## AWS RDS 인스턴스 생성  
*인스턴스 생성후 ec2 인스턴스에 연결*   
https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.MySQL.html  

*인스턴스 생성*  
https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/TUT_WebAppWithRDS.html  

*rds와 workbench 연동하기*  
https://aws.amazon.com/ko/premiumsupport/knowledge-center/connect-rds-mysql-workbench/  

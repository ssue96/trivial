# ec2 instance에서 rds mysql 접근할 때 Error: connect ETIMEDOUT 발생   
ETIMEDOUT 에러는 해당 주소로 연결조차 안 된다는 것이다. 처음에는 ec2 instance 보안 그룹에 3000번 포트 안 연 건 줄 알고 삽질을 했다.. (하다 하다 컴퓨터 껐다 킴..)  
진작 로그 좀 확인할걸... pm2 로그 찍히는 것을 보니 ETIMEDOUT이 발생하고 있었다. 일단 보안 그룹 검사부터 해야 한다

1. 보안 그룹 검사  

RDS 인스턴스의 보안 그룹이 원본 서버가 속한 보안 그룹 (또는 AWS 외부의 경우 IP가 직접 추가 된)의 액세스를 허용하는지 확인한다.
내 경우에는 처음에는 mysql workbench랑 연동이 안 되어서 보안 그룹 인바운드 규칙에 3306포트 - 내 ip만 추가한 게 문제였다..  
~~그래서 추가로 3306 포트에 위치 무관도 열어주었다. (과연 이게 맞을까..)~~  
인바운드 규칙 3306포트에 ec2 instance의 보안그룹 id를 추가해주었다.
아무튼, 이렇게 어이없는 에러가 해결되었다!

**참고**  
https://codeday.me/ko/qa/20190507/480975.html  

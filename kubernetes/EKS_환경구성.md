=====================================================
절차
1. eks cluster용 vpc 생성
2. 작업 환경(명령어 날릴 ec2) 소프트웨어 설치 - kubectl, aws cli 설치
3. eks cluster 생성(aws cli 이용)
4. worker node 접속

참고
https://docs.aws.amazon.com/eks/latest/userguide/getting-started-console.html
=====================================================


--------------------------------------------------------------------------------------
1. eks cluster용 vpc 생성
public subnet에 bastion과 nat 인스턴스 구성(두개 동시에 구성)
private subnet에 ec2 인스턴스 생성
--------------------------------------------------------------------------------------
bastion에서 private subnet에 생성된 인스턴스로 ssh 접속
1. bastion host에 key 전송(sftp 이용)
filezilla로 ec2 instance 연결 방법
https://forums.aws.amazon.com/message.jspa?messageID=757033

2. instance에 openssh 설치 확인
$ rpm -qa | grep openssh

3. private 인스턴스로 접속  
$ sudo ssh -i mission.pem(key pair) ec2-user@10.0.2.93(접속하고자 하는 ip)


1. prvivate public subnet생성과 vpc 구성
https://tech.cloud.nongshim.co.kr/2018/10/16/4-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EA%B5%AC%EC%84%B1%ED%95%98%EA%B8%B0vpc-subnet-route-table-internet-gateway/

2. nat 인스턴스 구성
https://tech.cloud.nongshim.co.kr/2018/10/16/%EC%B4%88%EB%B3%B4%EC%9E%90%EB%A5%BC-%EC%9C%84%ED%95%9C-aws-%EC%9B%B9%EA%B5%AC%EC%B6%95-5-1-nat-%EC%84%9C%EB%B2%84-%EC%83%9D%EC%84%B11/


--------------------------------------------------------------------------------------
2. 작업 환경(명령어 날릴 ec2) 소프트웨어 설치 - kubectl, aws cli 설치

참고
https://eksworkshop.com/020_prerequisites/k8stools/
--------------------------------------------------------------------------------------

--------------------------------------------------------------------------------------
3. eks cluster 생성(aws cli 이용)
aws eks create-cluster --name eks-cluster-test --kubernetes-version=1.14 --role-arn arn:aws:iam::536983495792:role/eksctl-eksworkshop-eksctl-cluster-ServiceRole-L7TS4O31K41Y --resources-vpc-config subnetIds=subnet-0896181ec2aadaccf,subnet-07b9944a3fe1fc97e,subnet-04a53e0ab447c7ec8,subnet-01b8ca4fae0419822,securityGroupIds=sg-098ce84f5a2b3cc9f --region ap-northeast-2

kube config update
aws eks update-kubeconfig --name eks-cluster-test --region ap-northeast-2
--------------------------------------------------------------------------------------

--------------------------------------------------------------------------------------
4. worker node 접속
cd .ssh
ssh -i id_rsa ec2-user@10.0.2.250
(id_rsa > ssh keygen으로 생성한 private key>node group 생성시 key를 이 키로 설정)

--------------------------------------------------------------------------------------

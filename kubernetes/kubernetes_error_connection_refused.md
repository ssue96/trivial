# kubernetes 실행 error - 
## The connection to the server localhost:8080 was refused - did you specify the right host or port?

  $kubectl get pods  
실행시 저 에러가 나는 이유는 configuration 설정 문제이다.  
kubernetes setup은 root 계정으로 하고 실행은 다른 계정에서 했기 때문에 발생하는 error  
이럴 땐 config 파일을 실행 중인 계정의 home 디렉터리로 복사하고 소유자를 변경하면 된다.  

  $mkdir -p $HOME/.kube  
  $ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config  
  $ sudo chown $(id -u):$(id -g) $HOME/.kube/config  

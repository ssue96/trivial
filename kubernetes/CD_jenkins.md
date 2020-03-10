# Jenkins를 이용한 ci/cd pipeline 구성  

**목차**  
[1. helm 설치](#1.-helm-설치)  
[2. jenkins 설치](#2.-jenkins-설치)  
[3. ci/cd pipeline 구성](#3.-ci/cd-pipeline-구성-)  

### 1. helm 설치  
**설치**  
```
$ curl -sSL https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```

**버전확인**  
```
$ helm version --short
```

**stable repository 다운**  
```
$ helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

**helm 자동완성**  
```
$ helm completion bash >> ~/.bash_completion
$ . /etc/profile.d/bash_completion.sh
$ . ~/.bash_completion
$ source <(helm completion bash)
```

### 2. jenkins 설치  

**helm으로 jenkins 설치**  
```
$ helm install cd stable/jenkins --set rbac.create=true,master.servicePort=80,master.serviceType=LoadBalancer  
(volume 자동생성하면 100gi짜리 만들어짐 value.yaml 들어가서 수정 필요)  

비밀번호 찾기  
$ printf $(kubectl get secret --namespace default cicd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
```

**external ip찾기**  
```
$ export SERVICE_IP=$(kubectl get svc --namespace default cicd-jenkins --template "{{ range (index .status.loadBalancer.ingress 0) }}{{ . }}{{ end }}")
$ echo http://$SERVICE_IP/logi
```

### 3. ci/cd pipeline 구성  
**jenkins에서 credential 추가**  
1. Username and password  
github 계정 id와 password로 추가

또는  

2. SSH Username with private key  
github 계정에 public key를 등록한 후 jenkins credential 추가시 private key 등록  
```
1. puttygen → generate key → (마우스 움직이면 난수 생성) → public key, private key 저장  
2. github → 내 repository → setting → deploy keys → add new deploy → key 부분에 public key 내용 복사
3. jenkins → credential → add credential → secret → private key 저장  
```
(build trigger 설정을 위한 작업)  
**jenkins github integration plugin 설치(build trigger에서 github webhook 사용하기 위해)**  
Manage Jenkins → Manage Plugins → Available → github integration → install without restart

**github webhook 설정**  
payload url → jenkins url/github-webhook 추가 → content type → application json → 저장  

**project 추가**  
new item → free style → git hub repository에 ssh 주소로 복사 → credential 에 추가한 credentail 선택 → build trigger GitHub hook trigger for GITScm polling 선택  


### 참고   
jenkins github 연동  
https://velog.io/@minholee_93/Jenkins-Integrate-with-GitHub-b2k5q9b61o  

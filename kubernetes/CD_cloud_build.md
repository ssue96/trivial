## Cloud Native DevOps with Kubernetes-14장 cloud build 예제  
구글 cloud build를 이용하여 CD 파이프라인을 구성한다. GKE 클러스터를 구성하고 클러스터의 helm을 초기화한다.  
그 후 다음의 단계로 파이프라인을 설정한다.  
1. 데모 저장소를 개인 깃허브 계정으로 포크  
2. 깃 브랜치로 푸시한 코드를 빌드하고 테스트하는 클라우드 **빌드 트리거 생성**  
3. 깃 태그를 기반으로 GKE에 배포하기 위한 **배포 트리거 생성**

### gke cluster 생성  
### 터미널 project ID설정과 cluster credentials 얻기  
```
$ gcloud config set project [project_id]
$ gcloud container clusters get-credentials standard-cluster-1 --zone asia-northeast1-a --project [project_id]
```
### git 브랜치에서 demo fork 
```
$ git clone "your repository" 
$ cd demo
```
### demo 앱 배포  

**helm 설치**
```
$ wget https://storage.googleapis.com/kubernetes-helm/helm-v2.14.1-linux-amd64.tar.gz
$ tar zxfv helm-v2.14.1-linux-amd64.tar.gz
$ cp linux-amd64/helm .

```
**계정에 cluster admin 자격 부여**  
```
$ kubectl create clusterrolebinding cluster-admin-binding --clusterrole=cluster-admin --user=$(gcloud config get-value account)
```
**클러스터에 접근할 수 있도록 권한을 부여하는 tiller 설정**  
```
$ kubectl create clusterrolebinding tiller-admin-binding --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
```
```
helm3부터는 tiller 사용 x  
v2는 클러스터에 helm chart 통합관리와 설치를 위해 Tiller라는 Pod를 사용했다.   
따라서 cluster-admin 권한을 부여했는데, production 환경까지 생각한다면 보안에는 좋지 않다.   
helm 클라이언트와 tiller는 gRPC를 사용해 통신하기 때문에 gRPC 포트가 디폴트로 열려있다. 보안 없이..   
따라서 공격자가 악의적인 의도로 내부 네트워크를 타고 gRPC 포트에 요청을 보내면 아무런   
인증 과정 없이 cluster-admin 권한을 사용할 수 있게 된다.   
v3은 Tiller를 완전히 삭제하고 쿠버네티스 API를 사용해 helm chart를 쿠버네티스에 저장한다.   
chart를 설치하는 RBAC 권한은 사용자가 사용한 kubeconfig 파일을 따라간다.  

참고 
https://kycfeel.github.io/2019/12/25/Helm-v2-%EC%97%90%EC%84%9C-v3-%EB%A1%9C-%EB%A7%88%EC%9D%B4%EA%B7%B8%EB%A0%88%EC%9D%B4%EC%85%98-%ED%95%98%EA%B8%B0/  

```
**helm 초기화** 
```
$ ./helm init --service-account=tiller
$ ./helm update
```


**helm 설치 확인** 
```
$ ./helm version
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
Server: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
```

**helm release** 
```
$ helm install --name demo ./k8s/demo/
```

**service 생성 확인 후 접속**  
```
$ kubectl get svc
NAME           TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
demo-service   ClusterIP      10.4.11.227   <none>          8888/TCP         12h

$ kubectl port-forward service/demo-service 9999:8888
$ curl http://localhost:9999
>hello, 世界
```

### 빌드 트리거 생성  
https://github.com/apps/google-cloud-build 에서 install > only select repositories > demo 선택  

cloud build console에서 빌드 트리거 생성  
* github(beta) 선택  
* demo 선택  
* trigger type : Branch
* build configuration: cloud build configruation file
* Cloud Build configuration file location : hello-cloudbuild/cloudbuild.yaml  

### 트리거 테스트
hello-> hola로 변경 후 클라우드 빌드 트리거 자동 실행 확인
```
$ git checkout -b hola
$ git push origin hola
```
빌드 성공 확인! 

### CD 파이프라인에서 배포 - 디플로이 트리거 생성(빌드 트리거 생성과 유사)
* github(beta) 선택  
* demo 선택  
* trigger type : Tag
* Build configuration: cloud build configruation file
* Cloud Build configuration file location: hello-cloudbuild/cloudbuild-deploy.yaml  
* Substitution variables : 
{
_CLOUDSDK_CORE_PROJECT: (프로젝트 ID)
_CLOUDSDK_COMPUTE_ZONE: asia-northeast1-a
_CLOUDSDK_CONTAINER_CLUSTER: standard-cluster-1(cluster 이름)
}



### 트리거 테스트  
staging과 production tag 붙여서 git에 push
```
$ git tag -f staging
$ git push -f origin refs/tags/staging
$ kubectl get service -n staging-demo
```
```
$ git tag -f production
$ git push -f origin refs/tags/production
$ kubectl get service -n production-demo
```
자동 배포 확인 > console에서 빌드 완료

### 버전 업 테스트  
```
$ kubectl --namespace port-forward service/demo-service 9999:8888
$ curl http://localhost:9999
>hola, 世界
```
## 참고  
https://medium.com/@jwlee98/gcp-gke-%EC%B0%A8%EA%B7%BC-%EC%B0%A8%EA%B7%BC-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0-5%ED%83%84-cloud-build-%EB%A5%BC-%ED%86%B5%ED%95%9C-ci-cd-47f1486937e4  

jenkins  
https://www.qwiklabs.com/focuses/1104?parent=catalog  

spinnaker  
https://www.qwiklabs.com/focuses/552?parent=catalog  

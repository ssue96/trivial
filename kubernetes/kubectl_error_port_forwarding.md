# kubectl port forwarding error 
**error : unable to do port forwarding: socat not found**  

    $ kubectl port-forward deploy/demo 9999:8888  
    
    Forwarding from 127.0.0.1:9999 -> 8888
    Forwarding from [::1]:9999 -> 8888
    Handling connection for 9999
    E0107 15:35:57.439283   32698 portforward.go:400]  
    an error occurred forwarding 9999 -> 8888: error forwarding port 8888 to pod e67b0967f8eff461de01fd7225cec6a6da41e4b48751c1fc00f4ceab50e903ef,  
    uid : unable to do port forwarding: socat not found  
  
socat이 설치되지 않아 발생하는 에러.  
socat을 설치한다.  
(os : debian)

    $ sudo apt-get install socat 
    $ kubectl port-forward deploy/demo 9999:8888  
    $ curl http://localhost:9999/
    >>hello word!  
    
이러면 제대로 응답이 온다.  

`스터디 중 kubernetes 위에 올리 컨테이너를 public ip로 직접 접근할 수 없다는 issue가 있었는데 다음엔 이를 좀 더 알아봐야겠다..`  
`centOS에 docker 설치 후 minikube 설치 시 minikube start error가 발생하는데 아마 cgroup 설정에서 문제가 발생하는 것 같다.  
https://kubernetes.io/ko/docs/setup/production-environment/`




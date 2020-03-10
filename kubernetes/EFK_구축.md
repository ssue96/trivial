1	nginx 서비스 배포	"nginx 서비스 배포
$ vi nginx-deployment.yaml
---
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
---
$ kubectl apply -f nginx-deployment.yaml
$ kubectl expose deployment/my-nginx --type=LoadBalancer --port=80"		"LoadBalancer의 DNS 주소로 확인 가능
acea025f759f411eabc0e02c81df2925-1884014884.ap-northeast-2.elb.amazonaws.com"	
2	elasticsearch 설치	"elastic search로 수집해서 모으기
$ vi ealasticSearch.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: elasticsearch
  labels:
    app: elasticsearch
spec:
  replicas: 1
  selector:
    matchLabels:
      app: elasticsearch
  template:
    metadata:
      labels:
        app: elasticsearch
    spec:
      containers:
      - name: elasticsearch
        image: elastic/elasticsearch:6.4.0
        env:
        - name: discovery.type
          value: ""single-node""
        ports:
        - containerPort: 9200
        - containerPort: 9300
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: elasticsearch
  name: elasticsearch-svc
  namespace: default
spec:
  ports:
  - name: elasticsearch-rest
    nodePort: 30920
    port: 9200
    protocol: TCP
    targetPort: 9200
  - name: elasticsearch-nodecom
    nodePort: 30930
    port: 9300
    protocol: TCP
    targetPort: 9300
  selector:
    app: elasticsearch
  type: NodePort


kubectl apply -f elasticSearch.yaml"		"(nginx 서비스 expose 하면서 생성된 clb 이용)
1. 로드밸런서 리스너 편집 > tcp 9200 tcp 30920
2. 로드밸런서 security group 편집 > (sg-0c1500dc2b4f88a46) inbound port 9200 열기
3. web에서 로드밸런서 dns:port로 접근
acea025f759f411eabc0e02c81df2925-1884014884.ap-northeast-2.elb.amazonaws.com:9200"	
3	kibana 설치	"kibana로 시각화
$ vi kibana.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kibana
  labels:
    app: kibana
spec:
  replicas: 1
  selector:
    matchLabels:
      app: kibana
  template:
    metadata:
      labels:
        app: kibana
    spec:
      containers:
      - name: kibana
        image: elastic/kibana:6.4.0
        env:
        - name: SERVER_NAME
          value: ""kibana.kubenetes.example.com""
        - name: ELASTICSEARCH_URL
          value: ""http://elasticsearch-svc.default.svc.cluster.local:9200""
        ports:
        - containerPort: 5601
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: kibana
  name: kibana-svc
  namespace: default
spec:
  ports:
  - nodePort: 30561
    port: 5601
    protocol: TCP
    targetPort: 5601
  selector:
    app: kibana
  type: NodePort

$ kubectl apply -f kibana.yaml"		"(nginx 서비스 expose 하면서 생성된 clb 이용)
1. 로드밸런서 리스너 편집 > tcp 5601 tcp 30561
2. 로드밸런서 security group 편집 > (sg-0c1500dc2b4f88a46) inbound port 5601 열기
3. web에서 로드밸런서 dns:port로 접근
acea025f759f411eabc0e02c81df2925-1884014884.ap-northeast-2.elb.amazonaws.com:5601"	
4	fluentd 이용해서 로그 수집	"fluentd 이용해서 로그 수집하기
$ vi fluentd-elasticsearch.yaml
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: fluentd
  namespace: kube-system

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: fluentd
  namespace: kube-system
rules:
- apiGroups:
  - """"
  resources:
  - pods
  - namespaces
  verbs:
  - get
  - list
  - watch

---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: fluentd
roleRef:
  kind: ClusterRole
  name: fluentd
  apiGroup: rbac.authorization.k8s.io
subjects:
- kind: ServiceAccount
  name: fluentd
  namespace: kube-system
---
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd
  namespace: kube-system
  labels:
    k8s-app: fluentd-logging
    version: v1
spec:
  selector:
    matchLabels:
      k8s-app: fluentd-logging
      version: v1
  template:
    metadata:
      labels:
        k8s-app: fluentd-logging
        version: v1
    spec:
      serviceAccount: fluentd
      serviceAccountName: fluentd
      tolerations:
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      containers:
      - name: fluentd
        image: fluent/fluentd-kubernetes-daemonset:v1-debian-elasticsearch
        env:
          - name:  FLUENT_ELASTICSEARCH_HOST
            value: ""elasticsearch-svc.default.svc.cluster.local""
          - name:  FLUENT_ELASTICSEARCH_PORT
            value: ""9200""
          - name: FLUENT_ELASTICSEARCH_SCHEME
            value: ""http""
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers"			"fluentd 는 daemonset. 
FLUENT_ELASTICSEARCH_HOST ""elasticsearch-svc.default.svc.cluster.local""로 해야함. 그래야 쿠버네티스가 내부적으로 제공하는 dns 서비스 사용하는 것임.

"
5	kibana에서 nginx 로그 시각화	"kibana에서 nginx 로그 시각화
키바나로 들어감>management에서 인덱스 패턴 생성
discover>filter>kubenetes.labels.run is my-nginx"			

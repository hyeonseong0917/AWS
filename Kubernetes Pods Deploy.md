# 실습 1 : Kubernetes Pod 배포



## 목표

- Kubernetes 애플리케이션 생성 및 배포
- 배포, 서비스 및 네임스페이스 리소스 빌드
- 네임스페이스에서 리소스 확인
- 서비스 및 배포 세부 정보 분석
- Pod의 세부정보 이해
- Pod에서 명령 실행



## 시나리오

kubectl(Kubernetes API 서버와 통신하기 위한 CLI)을 사용해 Amazon EKS 클러스터에 액세스

=> Kubernetes 애플리케이션 리소스 구현, Kubernetes Pod에서 실행 가능



* (CLI) => CLI Command



## 과제 1 : Bastion Host에 연결

1 . Bastion Host(Instance Name)를 Session Manager를 통해 connect

* 홈 디렉토리로 이동
  - cd~ (CLI)



## 과제 2 : Sample Kubernetes Application 배포

2 . kubectl 설치 확인

- kubectl version (CLI)



- (출력값)
  - Client Version: version.Info{Major:"1", Minor:"19", GitVersion:"v1.19.8", GitCommit:"fd5d41537aee486160ad9b5356a9d82363273721", GitTreeState:"clean", BuildDate:"2021-02-17T12:41:51Z",GoVersion:"go1.15.8", Compiler:"gc", Platform:"linux/amd64"} Server Version: version.Info{Major:"1", Minor:"19+", GitVersion:"v1.19.8-eks-96780e", GitCommit:"96780e1b30acbf0a52c38b6030d7853e575bcdf3", GitTreeState:"clean", BuildDate:"2021-03-10T21:32:29Z", GoVersion:"go1.15.8", Compiler:"gc", Platform:"linux/amd64"}
  - GitVersion : 현재 설치된 Git 버전
  - BuildDate : 빌드 날짜
  - Platform : 유틸리티가 실행중인 운영 체제



3 . Kubernetes 네임스페이스 생성

- kuberctl create namespace my-namespace
- 네임스페이스는 Kuberentes 가상 클러스터
- 많은 사용자가 분산되어 있는 환경에서 사용하기 위한 것



4 . Sample Application 배포 파일 생성(.yaml)

- cat << EOF > sample-service.yaml apiVersion: v1 kind: Service metadata:  name: my-service  namespace: my-namespace  labels:    app: my-app spec:  selector:    app: my-app  ports:    - protocol: TCP      port: 80      targetPort: 80 --- apiVersion: apps/v1 kind: Deployment metadata:  name: my-deployment  namespace: my-namespace  labels:    app: my-app spec:  replicas: 3  selector:    matchLabels:      app: my-app  template:    metadata:      labels:        app: my-app    spec:      containers:      - name: nginx        image: nginx:1.14.2        ports:        - containerPort: 80 EOF (CLI)
- 3개의 replicas가 있는 Sample nginx Application 생성
- Sample Service - Application을 네트워크로 노출하는 추상적인 방법



5 . Sample Application 배포 파일 적용

- kubectl apply -f sample-service.yaml (CLI)
- (kubectl apply -f ) Command는 원하는 배포 파일을 배포 컨트롤러로 전송



6 .  my-namespace 내의 리소스를 모두 확인 ( NAME, READY, STATUS, RESTARTS, AGE)

- kubectl get all -n my-namespace
- (Output)
  - NAME                                 READY   STATUS    RESTARTS   AGE pod/my-deployment-776d8f8fd8-78w66   1/1     Running   0          27m pod/my-deployment-776d8f8fd8-dkjfr   1/1     Running   0          27m pod/my-deployment-776d8f8fd8-wmqj6   1/1     Running   0          27m NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE service/my-service   ClusterIP   10.100.190.12   <none>        80/TCP    32m NAME                            READY   UP-TO-DATE   AVAILABLE   AGE deployment.apps/my-deployment   3/3     3            3           27m NAME                                       DESIRED   CURRENT   READY   AGE replicaset.apps/my-deployment-776d8f8fd8   3         3         3       27m



7 . 첫 번째 Pod를 이후에 사용할 환경 변수로 지정

- export MY_POD_NAME=$(kubectl get pods -n my-namespace -o jsonpath='{.items[0].metadata.name}') (CLI)



8 . 현재 네임스페이스 확인

- kubectl get namespace
- (Output) (NAME, STATUS, AGE)
  - NAME              STATUS   AGE default           Active   1d kube-node-lease   Active   1d kube-public       Active   1d kube-system       Active   1d my-namespace      Active   1d



## 과제 3 : Sample Application Resource 탐색

9 . Deploy된 서비스의 세부정보 확인

- kubectl -n my-namespace describe service my-service  (CLI)
- (Output)
  - Name:              my-service Namespace:         my-namespace Labels:            app=my-app Annotations:       kubectl.kubernetes.io/last-applied-configuration:                     {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"labels":{"app":"my-app"},"name":"my-service","namespace":"my-namespace"}... Selector:          app=my-app Type:              ClusterIP IP:                10.100.190.12 Port:              <unset>  80/TCP TargetPort:        80/TCP
- 메타데이터를 Kubernetes 객체와 연결하는데 Labels와 annotation 모두 사용
- Labels : 객체선택, 특정 객체모음 찾는데 사용
- annotation : Labels에 허용되지 않는 문자 포함 가능



10 . 이전에 배포된 포드 중 하나의 세부 정보 확인

- kubectl -n my-namespace describe pod $ {MY_POD_NAME}
- (Output)
  - Name:         my-deployment-776d8f8fd8-78w66 Namespace:    my-namespace Priority:     0 Node:         ip-192-168-9-36.us-west-2.compute.internal/192.168.9.36 IP:           192.168.16.57 IPs:  IP:           192.168.16.57 Controlled By:  ReplicaSet/my-deployment-776d8f8fd8 Conditions:  Type              Status  Initialized       True  Ready             True  ContainersReady   True  PodScheduled      True Events:  Type    Reason     Age    From                                                 Message  ----    ------     ----   ----                                                 -------  Normal  Scheduled  3m20s  default-scheduler                                    Successfully assigned my-namespace/my-deployment-776d8f8fd8-78w66 to ip-192-168-9-36.us-west-2.compute.internal
  - Replicaset : 특정 시점에 실행되는replicaSet Pod 세트를 안정적으로 유지



11 . pod에서 bash shell에 연결

- kubectl exec -it ${MY_POD_NAME} -n my-namespace -- /bin/bash  (CLI)
- (kubectl exec) Command는 컨테이너 내부에서 명령을 실행하여 애플리케이션을 검사하고 디버그 가능
- 컨테이너 내부에서 실행할 수 있는 Command들
  - ls / : 컨테이너 내부의 파일 및 디렉토리를 표시
  - cat /proc/mounts : 커널에서 직접 제공되는 /proc/mounts의 정보 표시



12 . DNS 확인자 구성 파일

- DNS 확인자 => 운영 체제의 Application이 Domain Name을 액세스하는데 필요한 IP 주소로 변환 가능
- cat/etc/resolv.conf (CLI)



namespace , pod를 탐색하고 pod에 직접 연결하여 Application 실행 완료 + kubectl describe를 통해 정보 확인



## 결론

- Kubernetes Application 생성 및 배포

- 배포, 서비스 및 네임스페이스 리소스 빌드

- 네임스페이스에서 리소스 확인

- 서비스 및 배포의 세부정보 분석

- Pod의 세부정보 분석

- Pod에서 명령 실행

  










## Módulo 6 - Evidências: do primeiro contêiner ao rollback em Kubernetes

### 1. Versões das Ferramentas
**Comandos executados:** `docker --version`, `kind --version` e `kubectl version --client`

```text
Client:
 Version:           28.0.4
 API version:       1.48
 Go version:        go1.23.7
 Git commit:        b8034c0
 Built:             Tue Mar 25 15:07:48 2025
 OS/Arch:           windows/amd64
 Context:           desktop-linux

Server: Docker Desktop 4.40.0 (187762)
 Engine:
  Version:          28.0.4
  API version:      1.48 (minimum version 1.24)
  Go version:       go1.23.7
  Git commit:       6430e49
  Built:            Tue Mar 25 15:07:22 2025
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.7.26
  GitCommit:        753481ec61c7c8955a23d6ff7bc8e4daed455734
 runc:
  Version:          1.2.5
  GitCommit:        v1.2.5-0-g59923ef
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0

kind v0.33.0 go1.26.7 windows/amd64

Client Version: v1.32.2
Kustomize Version: v5.5.0
```

### 2. DockerFile e deployment.yaml
```text
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt ./
RUN python -m pip install --no-cache-dir -r requirements.txt
COPY app.py ./
RUN useradd --create-home --uid 10001 app
USER app
EXPOSE 8000
CMD ["python", "-m", "uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000"]


apiVersion: apps/v1
kind: Deployment
metadata:
  name: hospital-api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: hospital-api
  template:
    metadata:
      labels:
        app: hospital-api
    spec:
      containers:
      - name: api
        image: hospital-api:1.0.0
        imagePullPolicy: Never
        ports:
        - containerPort: 8000
---
apiVersion: v1
kind: Service
metadata:
  name: hospital-api-service
spec:
  type: NodePort
  selector:
    app: hospital-api
  ports:
    - port: 8000
      targetPort: 8000
      nodePort: 30000
```

### 3. Saída da validação com --dry-run=client
**Comandos executados:** `kubectl apply -f deployment.yaml --dry-run=client`

```text
deployment.apps/hospital-api created (dry run)
service/hospital-api-service created (dry run)
```

### 4. Contexto kind-hospital-local confirmado.
**Comandos executados:** `kubectl config current-context`

```text
kind-hospital-local
```

### 5. Imagem carregada no cluster.
**Comandos executados:** `kubectl config current-context`

```text
kind-hospital-local
```

### 6. Rollout inicial concluído.
**Comandos executados:** `kubectl apply -f deployment.yaml`, `kubectl rollout status deployment/hospital-ap`

```text
deployment.apps/hospital-api created
service/hospital-api-service created
Waiting for deployment "hospital-api" rollout to finish: 0 of 3 updated replicas are available...
Waiting for deployment "hospital-api" rollout to finish: 1 of 3 updated replicas are available...
Waiting for deployment "hospital-api" rollout to finish: 2 of 3 updated replicas are available...
deployment "hospital-api" successfully rolled out
```

### 7. Lista de Pods e do Service.
**Comandos executados:** `kubectl get pods,svc`

```text
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   26m
```

### 9. listagem de Pods antes e depois de apagar um à mão.
**Comandos executados:** `kubectl get pods -n hospital`, `kubectl get pods -n hospital`, `kubectl get pods -n hospital`

```text
NAME                            READY   STATUS    RESTARTS   AGE
hospital-api-86455444c6-nzdqc   1/1     Running   0          29m
hospital-api-86455444c6-tmdpg   1/1     Running   0          29m

pod "hospital-api-86455444c6-nzdqc" deleted

NAME                            READY   STATUS    RESTARTS   AGE
hospital-api-86455444c6-tmdpg   1/1     Running   0          29m
hospital-api-86455444c6-tp7lx   0/1     Running   0          1s
```

### 10. Trecho do describe com ImagePullBackOff.
**Comandos executados:** `kubectl describe pod hospital-api-86455444c6-tmdpg -n hospital`

```text
Name:             hospital-api-86455444c6-tmdpg
Namespace:        hospital
Priority:         0
Service Account:  default
Node:             hospital-local-control-plane/172.18.0.2
Start Time:       Sun, 20 Sep 2026 11:42:45 -0300
Labels:           app=hospital-api
                  pod-template-hash=86455444c6
Annotations:      <none>
Status:           Running
IP:               10.244.0.6
IPs:
  IP:           10.244.0.6
Controlled By:  ReplicaSet/hospital-api-86455444c6
Containers:
  hospital-api:
    Container ID:   containerd://eed6983028a7daaca5a82a68a57be2116003f903b1226f2a2f28212309bd6e95
    Image:          hospital-api:1.0.0
    Image ID:       docker.io/library/import-2026-09-20@sha256:e33a4a4661bef6732cb0b612fd16859d9cb8bdaa2b798244881ec1adcf372b35
    Port:           8000/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sun, 20 Sep 2026 11:42:46 -0300
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     250m
      memory:  256Mi
    Requests:
      cpu:      100m
      memory:   128Mi
    Liveness:   http-get http://:http/health/live delay=5s timeout=1s period=5s #success=1 #failure=3
    Readiness:  http-get http://:http/health/ready delay=2s timeout=1s period=3s #success=1 #failure=3
    Environment Variables from:
      hospital-api-config  ConfigMap  Optional: false
    Environment:           <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-xzbqj (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-xzbqj:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  40m   default-scheduler  Successfully assigned hospital/hospital-api-86455444c6-tmdpg to hospital-local-control-plane
  Normal  Pulled     40m   kubelet            Container image "hospital-api:1.0.0" already present on machine and can be accessed by the pod
  Normal  Created    40m   kubelet            Container created
  Normal  Started    40m   kubelet            Container started
```

### 11. Comando e status do rollback.
**Comandos executados:** `kubectl rollout status deployment/hospital-api -n hospital`

```text
deployment "hospital-api" successfully rolled out
```

### 12. Resposta de /health/live após o rollback.
**Comandos executados:** `curl.exe --fail --silent http://127.0.0.1:18080/health/live`

```text
{"status":"live"}
```

### 13. Confirmação da remoção do cluster.
**Comandos executados:** `kind delete cluster --name hospital-local`

```text
Deleting cluster "hospital-local" ...
Deleted nodes: ["hospital-local-control-plane"]
```
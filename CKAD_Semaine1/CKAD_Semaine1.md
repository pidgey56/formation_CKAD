TP1 - Pods / Deployments / Services 

1. Pods imperatif 
Contexte : Création simple d'un pod en utilisant une commande. Observation de son cycle de vie, vérification des logs, suppression du pod. 

Comportement attendu : Pod Créé passant par étape ContainerCreating jusque Running. Démarrage dans le conteneur du pod du serveur nginx. 

Actions: 
$ k run nginx --image=nginx --restart=Never
pod/nginx created

$ k get pods -o wide
NAME    READY   STATUS              RESTARTS   AGE   IP       NODE       NOMINATED NODE   READINESS GATES
nginx   0/1     ContainerCreating   0          7s    <none>   minikube   <none>           <none>

$ k get pods -o wide
NAME    READY   STATUS    RESTARTS   AGE   IP           NODE       NOMINATED NODE   READINESS GATES
nginx   1/1     Running   0          14s   10.244.0.3   minikube   <none>           <none>

$ k describe pod nginx 
Name:             nginx
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Thu, 04 Dec 2025 16:37:41 +0100
Labels:           run=nginx
Annotations:      <none>
Status:           Running
IP:               10.244.0.3
IPs:
  IP:  10.244.0.3
Containers:
  nginx:
    Container ID:   docker://18379e6d132e9bae9f2af60185306f1cc041cefd183ebf26108c733b8cce6216
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:553f64aecdc31b5bf944521731cd70e35da4faed96b2b7548a3d8e2598c52a42
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Thu, 04 Dec 2025 16:37:52 +0100
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-q88p6 (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True
  Initialized                 True
  Ready                       True
  ContainersReady             True
  PodScheduled                True
Volumes:
  kube-api-access-q88p6:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  24s   default-scheduler  Successfully assigned default/nginx to minikube
  Normal  Pulling    23s   kubelet            Pulling image "nginx"
  Normal  Pulled     14s   kubelet            Successfully pulled image "nginx" in 9.821s (9.821s including waiting). Image size: 151863538 bytes.
  Normal  Created    13s   kubelet            Created container: nginx
  Normal  Started    13s   kubelet            Started container nginx

$ k logs nginx 
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2025/12/04 15:37:52 [notice] 1#1: using the "epoll" event method
2025/12/04 15:37:52 [notice] 1#1: nginx/1.29.3
2025/12/04 15:37:52 [notice] 1#1: built by gcc 14.2.0 (Debian 14.2.0-19)
2025/12/04 15:37:52 [notice] 1#1: OS: Linux 6.6.87.2-microsoft-standard-WSL2
2025/12/04 15:37:52 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2025/12/04 15:37:52 [notice] 1#1: start worker processes
2025/12/04 15:37:52 [notice] 1#1: start worker process 29
2025/12/04 15:37:52 [notice] 1#1: start worker process 30
2025/12/04 15:37:52 [notice] 1#1: start worker process 31
2025/12/04 15:37:52 [notice] 1#1: start worker process 32
2025/12/04 15:37:52 [notice] 1#1: start worker process 33
2025/12/04 15:37:52 [notice] 1#1: start worker process 34
2025/12/04 15:37:52 [notice] 1#1: start worker process 35
2025/12/04 15:37:52 [notice] 1#1: start worker process 36

$ k delete pod nginx
pod "nginx" deleted from default namespace

Comportement Actuel :
Le pod nginx est crée, passe de l'état ContainerCreating puis Running. Attribution d'une adresse IP 10.244.0.3 sur le noeud minikube. Les logs indique le démarrage et la configuration du serveur nginx. Enfin le pod est supprimé avec succès. 

2. Deployments 

Contexte: 
Création d'un déploiement, faire une montée du nombre de repllicas, mettre à jour l'image du conteneur. Ensuite, effectuer une vérification du statut du rollout et d'effectuer ensuite un rollback. 

Comportement attendu : 
Création d'un déploiement, puis mise en place de trois réplicats. Présence donc de 3 pods. Mise à jour de l'image vers celle de nginx 1.25. Le Rollout se termine avec succès et l'historique des modification est affiché. Le Rollback ramène le déploiment à sa précédente version. 

Action :
$ k create deployment web --image=nginx
deployment.apps/web created

$ k scale deployment web --replicas=3
deployment.apps/web scaled

$ k set image deployment web nginx=nginx:1.25
deployment.apps/web image updated

$ k rollout status deployment web
Waiting for deployment "web" rollout to finish: 2 out of 3 new replicas have been updated...
Waiting for deployment "web" rollout to finish: 2 out of 3 new replicas have been updated...
Waiting for deployment "web" rollout to finish: 2 out of 3 new replicas have been updated...
Waiting for deployment "web" rollout to finish: 1 old replicas are pending termination...
Waiting for deployment "web" rollout to finish: 1 old replicas are pending termination...
deployment "web" successfully rolled out

$ k rollout history deployment web
deployment.apps/web 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>


$ k rollout undo deployment web
deployment.apps/web rolled back

$ k get deployments -o wide
NAME   READY   UP-TO-DATE   AVAILABLE   AGE    CONTAINERS   IMAGES   SELECTOR
web    3/3     3            3           114s   nginx        nginx    app=web

Comportement Actuel :
Le déploiement web est bien créé et scaler à 3 replicas. L'image est correctement mis à jour et le Rollout se termine correctement. L'historique affiche deux révisions sans cause de changement. Le Rollback est effectué. Le déploiement final à 3 réplicats qui sont prêts avec l'image nginx initial. 

3. Services 

Contexte :
Exposer le déploiement web via un objet Service de type NodePort et un second objet service de type ClusterIp

Comportement attendu : 
Deux objets services sont crées, web et web-internal, respectivement NodePort et ClusterIP. Le service web est alors accéssible depuis l'exterieur du cluster via le port 80. Le service web-internal, n'est quant a lui accessible uniquement qu'à l'intérieur du cluster. 

Actions: 
$ k expose deployment web --type=NodePort --port=80
service/web exposed

$ k expose deployment web --type=ClusterIP --port=80 --name=web-internal
service/web-internal exposed

$ k get svc
NAME           TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes     ClusterIP   10.96.0.1        <none>        443/TCP        6m10s
web            NodePort    10.103.254.65    <none>        80:30519/TCP   31s
web-internal   ClusterIP   10.105.188.138   <none>        80/TCP         4s

$ k describe svc web
Name:                     web
Namespace:                default
Labels:                   app=web
Annotations:              <none>
Selector:                 app=web
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.103.254.65
IPs:                      10.103.254.65
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  30519/TCP
Endpoints:                10.244.0.10:80,10.244.0.11:80,10.244.0.12:80
Session Affinity:         None
External Traffic Policy:  Cluster
Internal Traffic Policy:  Cluster
Events:                   <none>

$ km service web --url
http://127.0.0.1:43183
❗  Because you are using a Docker driver on linux, the terminal needs to be open to run it.

Comportement Actuel: 
Le service web (NodePort) est créé avec l'IP : 10.103.254.65 et le nodePort 30519/TCP. Le service web-internal est créé avec l'IP 10.195.188.138. Le service web mappe au port 80 des 3 pods du déploiement et une url locale est fournit pour accédée depuis l'extérieur : http://127.0.0.1:42183


TP2 - Autonomie impérative

Contexte : 
Créer un pod et un déploiement, puis scaler le a 5 réplicats, induire une erreur d'image et revenir à la situation précédente avec l'image corrigée. 

Comportement attendu: 
Le pod nginx est créé, le Deployment front est créé avec 3 réplicas, puis 5 après l'upscale. La modification de l'image entraine la création de nouveaux pods qui n'arrivent pas a accéder à l'image. La correction via rollout corrige le soucis et enfin la création d'un service permet d'exposer l'application depuis l'extérieur. 

Actions: 
$ k run nginx --image=nginx
pod/nginx created

$ k create deployment front --image=nginx --replicas=3
deployment.apps/front created

$ k get deployment
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
front   3/3     3            3           7s

$ k get deployment -o yaml
apiVersion: v1
items:
- apiVersion: apps/v1
  kind: Deployment
  metadata:
    annotations:
      deployment.kubernetes.io/revision: "1"
    creationTimestamp: "2025-12-04T15:51:23Z"
    generation: 1
    labels:
      app: front
    name: front
    namespace: default
    resourceVersion: "641"
    uid: 1c45798b-6956-416f-ad8e-397eabaedac9
  spec:
    progressDeadlineSeconds: 600
    replicas: 3
    revisionHistoryLimit: 10
    selector:
      matchLabels:
        app: front
    strategy:
      rollingUpdate:
        maxSurge: 25%
        maxUnavailable: 25%
      type: RollingUpdate
    template:
      metadata:
        labels:
          app: front
      spec:
        containers:
        - image: nginx
          imagePullPolicy: Always
          name: nginx
          resources: {}
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
        dnsPolicy: ClusterFirst
        restartPolicy: Always
        schedulerName: default-scheduler
        securityContext: {}
        terminationGracePeriodSeconds: 30
  status:
    availableReplicas: 3
    conditions:
    - lastTransitionTime: "2025-12-04T15:51:30Z"
      lastUpdateTime: "2025-12-04T15:51:30Z"
      message: Deployment has minimum availability.
      reason: MinimumReplicasAvailable
      status: "True"
      type: Available
    - lastTransitionTime: "2025-12-04T15:51:23Z"
      lastUpdateTime: "2025-12-04T15:51:30Z"
      message: ReplicaSet "front-576d6dc6b7" has successfully progressed.
      reason: NewReplicaSetAvailable
      status: "True"
      type: Progressing
    observedGeneration: 1
    readyReplicas: 3
    replicas: 3
    updatedReplicas: 3
kind: List
metadata:
  resourceVersion: ""

$ k scale deployment front --replicas=5
deployment.apps/front scaled

$ k get deployment
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
front   5/5     5            5           49s

$ k get deployment -o yaml
apiVersion: v1
items:
- apiVersion: apps/v1
  kind: Deployment
  metadata:
    annotations:
      deployment.kubernetes.io/revision: "1"
    creationTimestamp: "2025-12-04T15:51:23Z"
    generation: 2
    labels:
      app: front
    name: front
    namespace: default
    resourceVersion: "705"
    uid: 1c45798b-6956-416f-ad8e-397eabaedac9
  spec:
    progressDeadlineSeconds: 600
    replicas: 5
    revisionHistoryLimit: 10
    selector:
      matchLabels:
        app: front
    strategy:
      rollingUpdate:
        maxSurge: 25%
        maxUnavailable: 25%
      type: RollingUpdate
    template:
      metadata:
        labels:
          app: front
      spec:
        containers:
        - image: nginx
          imagePullPolicy: Always
          name: nginx
          resources: {}
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
        dnsPolicy: ClusterFirst
        restartPolicy: Always
        schedulerName: default-scheduler
        securityContext: {}
        terminationGracePeriodSeconds: 30
  status:
    availableReplicas: 5
    conditions:
    - lastTransitionTime: "2025-12-04T15:51:23Z"
      lastUpdateTime: "2025-12-04T15:51:30Z"
      message: ReplicaSet "front-576d6dc6b7" has successfully progressed.
      reason: NewReplicaSetAvailable
      status: "True"
      type: Progressing
    - lastTransitionTime: "2025-12-04T15:52:10Z"
      lastUpdateTime: "2025-12-04T15:52:10Z"
      message: Deployment has minimum availability.
      reason: MinimumReplicasAvailable
      status: "True"
      type: Available
    observedGeneration: 2
    readyReplicas: 5
    replicas: 5
    updatedReplicas: 5
kind: List
metadata:
  resourceVersion: ""

$ k set image deployment front nginx=nginx:toto
deployment.apps/front image updated

$ k get deployment
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
front   4/5     3            4           107s

$ k describe pod front-7c55855d8c-rbqcc
Name:             front-7c55855d8c-rbqcc
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Thu, 04 Dec 2025 16:52:56 +0100
Labels:           app=front
                  pod-template-hash=7c55855d8c
Annotations:      <none>
Status:           Pending
IP:               10.244.0.10
IPs:
  IP:           10.244.0.10
Controlled By:  ReplicaSet/front-7c55855d8c
Containers:
  nginx:
    Container ID:
    Image:          nginx:toto
    Image ID:
    Port:           <none>
    Host Port:      <none>
    State:          Waiting
      Reason:       ImagePullBackOff
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-w4vbq (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True
  Initialized                 True
  Ready                       False
  ContainersReady             False
  PodScheduled                True
Volumes:
  kube-api-access-w4vbq:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age                From               Message
  ----     ------     ----               ----               -------
  Normal   Scheduled  80s                default-scheduler  Successfully assigned default/front-7c55855d8c-rbqcc to minikube
  Normal   Pulling    40s (x3 over 79s)  kubelet            Pulling image "nginx:toto"
  Warning  Failed     38s (x3 over 77s)  kubelet            Failed to pull image "nginx:toto": Error response from daemon: manifest for nginx:toto not found: manifest unknown: manifest unknown
  Warning  Failed     38s (x3 over 77s)  kubelet            Error: ErrImagePull
  Normal   BackOff    2s (x5 over 76s)   kubelet            Back-off pulling image "nginx:toto"
  Warning  Failed     2s (x5 over 76s)   kubelet            Error: ImagePullBackOff

$ k set image deployment nginx=nginx:1.25
error: resource(s) were provided, but no name was specified

$ k set image deployment front nginx=nginx:1.25
deployment.apps/front image updated

$ k set image deployment nginx=nginx:toto
error: resource(s) were provided, but no name was specified

$ k set image deployment front nginx=nginx:toto
deployment.apps/front image updated

$ k get pod
NAME                     READY   STATUS         RESTARTS   AGE
front-7c55855d8c-pj4rs   0/1     ErrImagePull   0          9s
front-7c55855d8c-q87qp   0/1     ErrImagePull   0          9s
front-7c55855d8c-vf475   0/1     ErrImagePull   0          9s
front-7cb86db69-g57t9    1/1     Running        0          30s
front-7cb86db69-hr5hs    1/1     Running        0          41s
front-7cb86db69-slngb    1/1     Running        0          41s
front-7cb86db69-zz85c    1/1     Running        0          41s
nginx                    1/1     Running        0          5m57s

$ k describe pod/front-7c55855d8c-vf475
Name:             front-7c55855d8c-vf475
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Thu, 04 Dec 2025 16:55:25 +0100
Labels:           app=front
                  pod-template-hash=7c55855d8c
Annotations:      <none>
Status:           Pending
IP:               10.244.0.21
IPs:
  IP:           10.244.0.21
Controlled By:  ReplicaSet/front-7c55855d8c
Containers:
  nginx:
    Container ID:
    Image:          nginx:toto
    Image ID:
    Port:           <none>
    Host Port:      <none>
    State:          Waiting
      Reason:       ImagePullBackOff
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-gr8jv (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True
  Initialized                 True
  Ready                       False
  ContainersReady             False
  PodScheduled                True
Volumes:
  kube-api-access-gr8jv:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age                From               Message
  ----     ------     ----               ----               -------
  Normal   Scheduled  34s                default-scheduler  Successfully assigned default/front-7c55855d8c-vf475 to minikube
  Normal   BackOff    27s                kubelet            Back-off pulling image "nginx:toto"
  Warning  Failed     27s                kubelet            Error: ImagePullBackOff
  Normal   Pulling    14s (x2 over 33s)  kubelet            Pulling image "nginx:toto"
  Warning  Failed     9s (x2 over 27s)   kubelet            Failed to pull image "nginx:toto": Error response from daemon: manifest for nginx:toto not found: manifest unknown: manifest unknown
  Warning  Failed     9s (x2 over 27s)   kubelet            Error: ErrImagePull


$ k logs pod/front-7c55855d8c-vf475
Error from server (BadRequest): container "nginx" in pod "front-7c55855d8c-vf475" is waiting to start: image can't be pulled


$ k events pod/front-7c55855d8c-vf475
LAST SEEN               TYPE      REASON                    OBJECT                        MESSAGE
8m12s                   Normal    Starting                  Node/minikube                 Starting kubelet.
8m12s (x8 over 8m12s)   Normal    NodeHasSufficientMemory   Node/minikube                 Node minikube status is now: NodeHasSufficientMemory   
8m12s (x8 over 8m12s)   Normal    NodeHasNoDiskPressure     Node/minikube                 Node minikube status is now: NodeHasNoDiskPressure     
8m12s (x7 over 8m12s)   Normal    NodeHasSufficientPID      Node/minikube                 Node minikube status is now: NodeHasSufficientPID
8m12s                   Normal    NodeAllocatableEnforced   Node/minikube                 Updated Node Allocatable limit across pods
8m6s                    Normal    Starting                  Node/minikube                 Starting kubelet.
8m6s                    Normal    NodeAllocatableEnforced   Node/minikube                 Updated Node Allocatable limit across pods
8m6s                    Normal    NodeHasSufficientMemory   Node/minikube                 Node minikube status is now: NodeHasSufficientMemory   
8m6s                    Normal    NodeHasNoDiskPressure     Node/minikube                 Node minikube status is now: NodeHasNoDiskPressure     
8m6s                    Normal    NodeHasSufficientPID      Node/minikube                 Node minikube status is now: NodeHasSufficientPID      
8m2s                    Normal    RegisteredNode            Node/minikube                 Node minikube event: Registered Node minikube in Controller
8m                      Normal    Starting                  Node/minikube
7m37s                   Normal    Scheduled                 Pod/pod                       Successfully assigned default/pod to minikube
7m27s                   Normal    Pulled                    Pod/pod                       Successfully pulled image "nginx" in 8.626s (8.626s including waiting). Image size: 151863538 bytes.
7m26s (x2 over 7m36s)   Normal    Pulling                   Pod/pod                       Pulling image "nginx"
7m24s                   Normal    Pulled                    Pod/pod                       Successfully pulled image "nginx" in 1.696s (1.696s including waiting). Image size: 151863538 bytes.
7m24s (x2 over 7m27s)   Normal    Created                   Pod/pod                       Created container: pod
7m24s (x2 over 7m27s)   Normal    Started                   Pod/pod                       Started container pod
7m23s (x2 over 7m24s)   Warning   BackOff                   Pod/pod                       Back-off restarting failed container pod in pod pod_default(3eb60136-33cf-4f83-a850-364d23e69de1)
6m46s                   Normal    Scheduled                 Pod/nginx                     Successfully assigned default/nginx to minikube        
6m46s                   Normal    Pulling                   Pod/nginx                     Pulling image "nginx"
6m44s                   Normal    Pulled                    Pod/nginx                     Successfully pulled image "nginx" in 1.664s (1.664s including waiting). Image size: 151863538 bytes.
6m44s                   Normal    Created                   Pod/nginx                     Created container: nginx
6m44s                   Normal    Started                   Pod/nginx                     Started container nginx
5m                      Normal    SuccessfulCreate          ReplicaSet/front-576d6dc6b7   Created pod: front-576d6dc6b7-kw69j
5m                      Normal    Pulling                   Pod/front-576d6dc6b7-g8ps2    Pulling image "nginx"
5m                      Normal    Pulling                   Pod/front-576d6dc6b7-twlqj    Pulling image "nginx"
5m                      Normal    Pulling                   Pod/front-576d6dc6b7-kw69j    Pulling image "nginx"
5m                      Normal    Scheduled                 Pod/front-576d6dc6b7-g8ps2    Successfully assigned default/front-576d6dc6b7-g8ps2 to minikube
5m                      Normal    Scheduled                 Pod/front-576d6dc6b7-twlqj    Successfully assigned default/front-576d6dc6b7-twlqj to minikube
5m                      Normal    ScalingReplicaSet         Deployment/front              Scaled up replica set front-576d6dc6b7 from 0 to 3     
5m                      Normal    SuccessfulCreate          ReplicaSet/front-576d6dc6b7   Created pod: front-576d6dc6b7-twlqj
5m                      Normal    SuccessfulCreate          ReplicaSet/front-576d6dc6b7   Created pod: front-576d6dc6b7-g8ps2
5m                      Normal    Scheduled                 Pod/front-576d6dc6b7-kw69j    Successfully assigned default/front-576d6dc6b7-kw69j to minikube
4m58s                   Normal    Pulled                    Pod/front-576d6dc6b7-kw69j    Successfully pulled image "nginx" in 1.592s (1.593s including waiting). Image size: 151863538 bytes.
4m58s                   Normal    Started                   Pod/front-576d6dc6b7-kw69j    Started container nginx
4m58s                   Normal    Created                   Pod/front-576d6dc6b7-kw69j    Created container: nginx
4m57s                   Normal    Pulled                    Pod/front-576d6dc6b7-twlqj    Successfully pulled image "nginx" in 1.465s (3.058s including waiting). Image size: 151863538 bytes.
4m57s                   Normal    Created                   Pod/front-576d6dc6b7-twlqj    Created container: nginx
4m56s                   Normal    Started                   Pod/front-576d6dc6b7-twlqj    Started container nginx
4m55s                   Normal    Pulled                    Pod/front-576d6dc6b7-g8ps2    Successfully pulled image "nginx" in 1.881s (4.937s including waiting). Image size: 151863538 bytes.
4m55s                   Normal    Created                   Pod/front-576d6dc6b7-g8ps2    Created container: nginx
4m55s                   Normal    Started                   Pod/front-576d6dc6b7-g8ps2    Started container nginx
4m17s                   Normal    SuccessfulCreate          ReplicaSet/front-576d6dc6b7   Created pod: front-576d6dc6b7-hxkl2
4m17s                   Normal    Scheduled                 Pod/front-576d6dc6b7-8n9h2    Successfully assigned default/front-576d6dc6b7-8n9h2 to minikube
4m17s                   Normal    ScalingReplicaSet         Deployment/front              Scaled up replica set front-576d6dc6b7 from 3 to 5     
4m17s                   Normal    SuccessfulCreate          ReplicaSet/front-576d6dc6b7   Created pod: front-576d6dc6b7-8n9h2
4m17s                   Normal    Scheduled                 Pod/front-576d6dc6b7-hxkl2    Successfully assigned default/front-576d6dc6b7-hxkl2 to minikube
4m16s                   Normal    Pulling                   Pod/front-576d6dc6b7-8n9h2    Pulling image "nginx"
4m16s                   Normal    Pulling                   Pod/front-576d6dc6b7-hxkl2    Pulling image "nginx"
4m15s                   Normal    Pulled                    Pod/front-576d6dc6b7-8n9h2    Successfully pulled image "nginx" in 1.718s (1.718s including waiting). Image size: 151863538 bytes.
4m14s                   Normal    Started                   Pod/front-576d6dc6b7-8n9h2    Started container nginx
4m14s                   Normal    Created                   Pod/front-576d6dc6b7-8n9h2    Created container: nginx
4m13s                   Normal    Pulled                    Pod/front-576d6dc6b7-hxkl2    Successfully pulled image "nginx" in 1.55s (3.261s including waiting). Image size: 151863538 bytes.
4m13s                   Normal    Created                   Pod/front-576d6dc6b7-hxkl2    Created container: nginx
4m13s                   Normal    Started                   Pod/front-576d6dc6b7-hxkl2    Started container nginx
3m27s                   Normal    ScalingReplicaSet         Deployment/front              Scaled down replica set front-576d6dc6b7 from 5 to 4   
3m27s                   Normal    Scheduled                 Pod/front-7c55855d8c-rbqcc    Successfully assigned default/front-7c55855d8c-rbqcc to minikube
3m27s                   Normal    SuccessfulCreate          ReplicaSet/front-7c55855d8c   Created pod: front-7c55855d8c-cp4zs
3m27s                   Normal    SuccessfulDelete          ReplicaSet/front-576d6dc6b7   Deleted pod: front-576d6dc6b7-8n9h2
3m27s                   Normal    Scheduled                 Pod/front-7c55855d8c-9xlm5    Successfully assigned default/front-7c55855d8c-9xlm5 to minikube
3m27s                   Normal    SuccessfulCreate          ReplicaSet/front-7c55855d8c   Created pod: front-7c55855d8c-9xlm5
3m27s                   Normal    Scheduled                 Pod/front-7c55855d8c-cp4zs    Successfully assigned default/front-7c55855d8c-cp4zs to minikube
3m27s                   Normal    Killing                   Pod/front-576d6dc6b7-8n9h2    Stopping container nginx
3m27s                   Normal    SuccessfulCreate          ReplicaSet/front-7c55855d8c   Created pod: front-7c55855d8c-rbqcc
3m19s                   Normal    SandboxChanged            Pod/front-7c55855d8c-9xlm5    Pod sandbox changed, it will be killed and re-created. 
115s (x4 over 3m26s)    Normal    Pulling                   Pod/front-7c55855d8c-rbqcc    Pulling image "nginx:toto"
113s (x4 over 3m24s)    Warning   Failed                    Pod/front-7c55855d8c-rbqcc    Failed to pull image "nginx:toto": Error response from daemon: manifest for nginx:toto not found: manifest unknown: manifest unknown
113s (x4 over 3m24s)    Warning   Failed                    Pod/front-7c55855d8c-rbqcc    Error: ErrImagePull
112s (x4 over 3m26s)    Normal    Pulling                   Pod/front-7c55855d8c-cp4zs    Pulling image "nginx:toto"
111s (x7 over 3m18s)    Normal    BackOff                   Pod/front-7c55855d8c-9xlm5    Back-off pulling image "nginx:toto"
111s (x7 over 3m18s)    Warning   Failed                    Pod/front-7c55855d8c-9xlm5    Error: ImagePullBackOff
110s (x4 over 3m22s)    Warning   Failed                    Pod/front-7c55855d8c-cp4zs    Failed to pull image "nginx:toto": Error response from daemon: manifest for nginx:toto not found: manifest unknown: manifest unknown
110s (x4 over 3m22s)    Warning   Failed                    Pod/front-7c55855d8c-cp4zs    Error: ErrImagePull
102s (x6 over 3m23s)    Normal    BackOff                   Pod/front-7c55855d8c-rbqcc    Back-off pulling image "nginx:toto"
102s (x6 over 3m23s)    Warning   Failed                    Pod/front-7c55855d8c-rbqcc    Error: ImagePullBackOff
100s (x6 over 3m21s)    Warning   Failed                    Pod/front-7c55855d8c-cp4zs    Error: ImagePullBackOff
100s (x6 over 3m21s)    Normal    BackOff                   Pod/front-7c55855d8c-cp4zs    Back-off pulling image "nginx:toto"
97s (x4 over 3m26s)     Normal    Pulling                   Pod/front-7c55855d8c-9xlm5    Pulling image "nginx:toto"
95s (x4 over 3m20s)     Warning   Failed                    Pod/front-7c55855d8c-9xlm5    Failed to pull image "nginx:toto": Error response from daemon: manifest for nginx:toto not found: manifest unknown: manifest unknown
95s (x4 over 3m20s)     Warning   Failed                    Pod/front-7c55855d8c-9xlm5    Error: ErrImagePull
90s                     Normal    SuccessfulCreate          ReplicaSet/front-7cb86db69    Created pod: front-7cb86db69-zz85c
90s                     Normal    ScalingReplicaSet         Deployment/front              Scaled up replica set front-7cb86db69 from 0 to 3      
90s                     Normal    SuccessfulDelete          ReplicaSet/front-7c55855d8c   Deleted pod: front-7c55855d8c-cp4zs
90s                     Normal    SuccessfulDelete          ReplicaSet/front-7c55855d8c   Deleted pod: front-7c55855d8c-9xlm5
90s                     Normal    Pulling                   Pod/front-7cb86db69-hr5hs     Pulling image "nginx:1.25"
90s                     Normal    SuccessfulCreate          ReplicaSet/front-7cb86db69    Created pod: front-7cb86db69-slngb
90s                     Normal    Pulling                   Pod/front-7cb86db69-zz85c     Pulling image "nginx:1.25"
90s                     Normal    Scheduled                 Pod/front-7cb86db69-zz85c     Successfully assigned default/front-7cb86db69-zz85c to minikube
90s                     Normal    Scheduled                 Pod/front-7cb86db69-hr5hs     Successfully assigned default/front-7cb86db69-hr5hs to minikube
90s                     Normal    SuccessfulDelete          ReplicaSet/front-7c55855d8c   Deleted pod: front-7c55855d8c-rbqcc
90s                     Normal    ScalingReplicaSet         Deployment/front              Scaled down replica set front-7c55855d8c from 3 to 0   
90s                     Normal    Scheduled                 Pod/front-7cb86db69-slngb     Successfully assigned default/front-7cb86db69-slngb to minikube
90s                     Normal    SuccessfulCreate          ReplicaSet/front-7cb86db69    Created pod: front-7cb86db69-hr5hs
90s                     Normal    Pulling                   Pod/front-7cb86db69-slngb     Pulling image "nginx:1.25"
80s                     Normal    Pulled                    Pod/front-7cb86db69-slngb     Successfully pulled image "nginx:1.25" in 9.215s (9.215s including waiting). Image size: 187659947 bytes.
80s                     Normal    Created                   Pod/front-7cb86db69-slngb     Created container: nginx
80s                     Normal    Started                   Pod/front-7cb86db69-slngb     Started container nginx
79s                     Normal    SuccessfulDelete          ReplicaSet/front-576d6dc6b7   Deleted pod: front-576d6dc6b7-g8ps2
79s                     Normal    ScalingReplicaSet         Deployment/front              Scaled down replica set front-576d6dc6b7 from 4 to 3   
79s                     Normal    Created                   Pod/front-7cb86db69-hr5hs     Created container: nginx
79s                     Normal    SuccessfulCreate          ReplicaSet/front-7cb86db69    Created pod: front-7cb86db69-g57t9
79s                     Normal    Killing                   Pod/front-576d6dc6b7-g8ps2    Stopping container nginx
79s                     Normal    Pulled                    Pod/front-7cb86db69-hr5hs     Successfully pulled image "nginx:1.25" in 1.725s (10.923s including waiting). Image size: 187659947 bytes.
79s                     Normal    ScalingReplicaSet         Deployment/front              Scaled up replica set front-7cb86db69 from 3 to 4      
79s                     Normal    Scheduled                 Pod/front-7cb86db69-g57t9     Successfully assigned default/front-7cb86db69-g57t9 to minikube
78s                     Normal    SuccessfulDelete          ReplicaSet/front-576d6dc6b7   Deleted pod: front-576d6dc6b7-kw69j
78s                     Normal    Pulling                   Pod/front-7cb86db69-g57t9     Pulling image "nginx:1.25"
78s                     Normal    Scheduled                 Pod/front-7cb86db69-d8cg7     Successfully assigned default/front-7cb86db69-d8cg7 to minikube
78s                     Normal    Killing                   Pod/front-576d6dc6b7-kw69j    Stopping container nginx
78s                     Normal    SuccessfulCreate          ReplicaSet/front-7cb86db69    Created pod: front-7cb86db69-d8cg7
78s                     Normal    Started                   Pod/front-7cb86db69-hr5hs     Started container nginx
77s                     Normal    Pulling                   Pod/front-7cb86db69-d8cg7     Pulling image "nginx:1.25"
77s                     Normal    Pulled                    Pod/front-7cb86db69-zz85c     Successfully pulled image "nginx:1.25" in 1.468s (12.391s including waiting). Image size: 187659947 bytes.
77s                     Normal    Created                   Pod/front-7cb86db69-zz85c     Created container: nginx
77s                     Normal    Started                   Pod/front-7cb86db69-zz85c     Started container nginx
76s                     Normal    SuccessfulDelete          ReplicaSet/front-576d6dc6b7   Deleted pod: front-576d6dc6b7-twlqj
76s                     Normal    Started                   Pod/front-7cb86db69-g57t9     Started container nginx
76s                     Normal    Created                   Pod/front-7cb86db69-g57t9     Created container: nginx
76s                     Normal    Pulled                    Pod/front-7cb86db69-g57t9     Successfully pulled image "nginx:1.25" in 1.444s (2.771s including waiting). Image size: 187659947 bytes.
76s                     Normal    Killing                   Pod/front-576d6dc6b7-twlqj    Stopping container nginx
75s                     Normal    SuccessfulDelete          ReplicaSet/front-576d6dc6b7   Deleted pod: front-576d6dc6b7-hxkl2
75s                     Normal    Killing                   Pod/front-576d6dc6b7-hxkl2    Stopping container nginx
74s                     Normal    Pulled                    Pod/front-7cb86db69-d8cg7     Successfully pulled image "nginx:1.25" in 1.509s (3.152s including waiting). Image size: 187659947 bytes.
74s                     Normal    Started                   Pod/front-7cb86db69-d8cg7     Started container nginx
74s                     Normal    Created                   Pod/front-7cb86db69-d8cg7     Created container: nginx
58s                     Normal    Killing                   Pod/front-7cb86db69-d8cg7     Stopping container nginx
58s                     Normal    Scheduled                 Pod/front-7c55855d8c-pj4rs    Successfully assigned default/front-7c55855d8c-pj4rs to minikube
58s                     Normal    SuccessfulCreate          ReplicaSet/front-7c55855d8c   Created pod: front-7c55855d8c-vf475
58s                     Normal    SuccessfulCreate          ReplicaSet/front-7c55855d8c   Created pod: front-7c55855d8c-q87qp
58s                     Normal    SuccessfulCreate          ReplicaSet/front-7c55855d8c   Created pod: front-7c55855d8c-pj4rs
58s                     Normal    Scheduled                 Pod/front-7c55855d8c-q87qp    Successfully assigned default/front-7c55855d8c-q87qp to minikube
58s                     Normal    SuccessfulDelete          ReplicaSet/front-7cb86db69    Deleted pod: front-7cb86db69-d8cg7
58s (x5 over 78s)       Normal    ScalingReplicaSet         Deployment/front              (combined from similar events): Scaled down replica set front-7cb86db69 from 5 to 4
58s (x2 over 3m27s)     Normal    ScalingReplicaSet         Deployment/front              Scaled up replica set front-7c55855d8c from 2 to 3     
58s (x2 over 3m27s)     Normal    ScalingReplicaSet         Deployment/front              Scaled up replica set front-7c55855d8c from 0 to 2     
58s                     Normal    Scheduled                 Pod/front-7c55855d8c-vf475    Successfully assigned default/front-7c55855d8c-vf475 to minikube
26s (x2 over 55s)       Warning   Failed                    Pod/front-7c55855d8c-q87qp    Error: ImagePullBackOff
26s (x2 over 55s)       Normal    BackOff                   Pod/front-7c55855d8c-q87qp    Back-off pulling image "nginx:toto"
23s (x2 over 53s)       Warning   Failed                    Pod/front-7c55855d8c-pj4rs    Error: ImagePullBackOff
23s (x2 over 53s)       Normal    BackOff                   Pod/front-7c55855d8c-pj4rs    Back-off pulling image "nginx:toto"
21s (x2 over 51s)       Normal    BackOff                   Pod/front-7c55855d8c-vf475    Back-off pulling image "nginx:toto"
21s (x2 over 51s)       Warning   Failed                    Pod/front-7c55855d8c-vf475    Error: ImagePullBackOff
12s (x3 over 57s)       Normal    Pulling                   Pod/front-7c55855d8c-q87qp    Pulling image "nginx:toto"
10s (x3 over 55s)       Warning   Failed                    Pod/front-7c55855d8c-q87qp    Failed to pull image "nginx:toto": Error response from daemon: manifest for nginx:toto not found: manifest unknown: manifest unknown
10s (x3 over 55s)       Warning   Failed                    Pod/front-7c55855d8c-q87qp    Error: ErrImagePull
9s (x3 over 57s)        Normal    Pulling                   Pod/front-7c55855d8c-pj4rs    Pulling image "nginx:toto"
8s (x3 over 57s)        Normal    Pulling                   Pod/front-7c55855d8c-vf475    Pulling image "nginx:toto"
7s (x3 over 53s)        Warning   Failed                    Pod/front-7c55855d8c-pj4rs    Error: ErrImagePull
7s (x3 over 53s)        Warning   Failed                    Pod/front-7c55855d8c-pj4rs    Failed to pull image "nginx:toto": Error response from daemon: manifest for nginx:toto not found: manifest unknown: manifest unknown
6s (x3 over 51s)        Warning   Failed                    Pod/front-7c55855d8c-vf475    Failed to pull image "nginx:toto": Error response from daemon: manifest for nginx:toto not found: manifest unknown: manifest unknown
6s (x3 over 51s)        Warning   Failed                    Pod/front-7c55855d8c-vf475    Error: ErrImagePull

$ k set image deployment front nginx=nginx:1.25
deployment.apps/front image updated

$ k get pod
NAME                    READY   STATUS    RESTARTS   AGE
front-7cb86db69-g57t9   1/1     Running   0          2m13s
front-7cb86db69-hr5hs   1/1     Running   0          2m24s
front-7cb86db69-qnvtt   1/1     Running   0          10s
front-7cb86db69-slngb   1/1     Running   0          2m24s
front-7cb86db69-zz85c   1/1     Running   0          2m24s
nginx                   1/1     Running   0          7m40s

Comportement Actuel : 
Le pod nginx est créé. Le déploiement front est créé avec 3 réplicats prets. Le scaling est appliqué et le déploiement atteint 5/5 réplicas prêts. La modification de l'image en une mauvaise entraine une impossibilité de créer un nouveau pod fonctionnel (4/5) pour remplacer les autres sous l'image précédente. La correction remet l'image originale et les pods atteignent le 5/5. Le Service est finalement exposé via la création d'un service de Type NodePort. 

TP3 Config / Secrets 

Contexte : 
Créer une configMap et un Secret pour injecter des variables d'environnement dans un pod. 

Comportement attendu :
Le configMap app-config et le secret db-secret sont créés. Le pod app-pod est créé et la commande grep confirme que les variables ENV (depuis ConfigMap) et DB_PASSWORD (depuis Secret) sont bien configurées pour le conteneur app. 

Actions: 
$ k create configmap app-config --from-literal=ENV=prod -o yaml --dry-run=client > configmap.yaml
$ k create secret generic db-secret --from-literal=password=admin123 -o yaml --dry-run=client > secret.yaml
$ k apply -f configmap.yaml -f secret.yaml
configmap/app-config created
secret/db-secret created
$ k get configmap,secret
NAME                         DATA   AGE
configmap/app-config         1      7s
configmap/kube-root-ca.crt   1      37s

NAME               TYPE     DATA   AGE
secret/db-secret   Opaque   1      7s
$ k apply -f pod-config.yaml && k get pod -o yaml | grep -A5 ENV
pod/app-pod created
        {"apiVersion":"v1","kind":"Pod","metadata":{"annotations":{},"name":"app-pod","namespace":"default"},"spec":{"containers":[{"env":[{"name":"ENV","valueFrom":{"configMapKeyRef":{"key":"ENV","name":"app-config"}}},{"name":"DB_PASSWORD","valueFrom":{"secretKeyRef":{"key":"password","name":"db-secret"}}}],"image":"nginx","name":"app"}]}}
    creationTimestamp: "2025-12-05T10:11:27Z"
    generation: 1
    name: app-pod
    namespace: default
    resourceVersion: "448"
--
      - name: ENV
        valueFrom:
          configMapKeyRef:
            key: ENV
            name: app-config
      - name: DB_PASSWORD
        valueFrom:
          secretKeyRef:
            key: password

Comportement Actuel: 
La configmap et le secret sont créés. La configmap app-config a 1 donnée, de même pour le secret db-secret. Le pod app-pod est créé. La vérification confirme l'injection. 


TP4 Probes

Contexte : 
Création d'un déploiement avec des liveness et readiness probes qui permettent de vérifier l'état de vie et de préparation du conteneur d'application. 

Comportement attendu : 
Le déploiement probed-app est créé, ce qui génère les 3 pods. La description des pods indiquera alors la configuration des sondes pour le conteneur nginx. 

Action: 
$ k apply -f deployment-probes.yaml && k describe pod
deployment.apps/probed-app created
Name:             app-pod
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Fri, 05 Dec 2025 11:11:27 +0100
Labels:           <none>
Annotations:      <none>
Status:           Running
IP:               10.244.0.3
IPs:
  IP:  10.244.0.3
Containers:
  app:
    Container ID:   docker://cdc05527d598fbc59b5efb518e81c5e6f3370d2b618dd80207f548216d00cf8e
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:553f64aecdc31b5bf944521731cd70e35da4faed96b2b7548a3d8e2598c52a42
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Fri, 05 Dec 2025 11:11:36 +0100
    Ready:          True
    Restart Count:  0
    Environment:
      ENV:          <set to the key 'ENV' of config map 'app-config'>  Optional: false
      DB_PASSWORD:  <set to the key 'password' in secret 'db-secret'>  Optional: false
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-mjfbp (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True
  Initialized                 True
  Ready                       True
  ContainersReady             True
  PodScheduled                True
Volumes:
  kube-api-access-mjfbp:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  14m   default-scheduler  Successfully assigned default/app-pod to minikube
  Normal  Pulling    14m   kubelet            Pulling image "nginx"
  Normal  Pulled     14m   kubelet            Successfully pulled image "nginx" in 8.33s (8.33s including waiting). Image size: 151863538 bytes. 
  Normal  Created    14m   kubelet            Created container: app
  Normal  Started    14m   kubelet            Started container app


Name:             probed-app-78b8c5fd96-b4rb6
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Fri, 05 Dec 2025 11:25:55 +0100
Labels:           app=probed-app
                  pod-template-hash=78b8c5fd96
Annotations:      <none>
Status:           Pending
IP:
IPs:              <none>
Controlled By:    ReplicaSet/probed-app-78b8c5fd96
Containers:
  nginx:
    Container ID:
    Image:          nginx
    Image ID:
    Port:           <none>
    Host Port:      <none>
    State:          Waiting
      Reason:       ContainerCreating
    Ready:          False
    Restart Count:  0
    Limits:
      cpu:     200m
      memory:  256Mi
    Requests:
      cpu:        100m
      memory:     128Mi
    Liveness:     http-get http://:80/ delay=5s timeout=1s period=10s #success=1 #failure=3
    Readiness:    http-get http://:80/ delay=3s timeout=1s period=10s #success=1 #failure=3
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-wxxrs (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   False
  Initialized                 True
  Ready                       False
  ContainersReady             False
  PodScheduled                True
Volumes:
  kube-api-access-wxxrs:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  0s    default-scheduler  Successfully assigned default/probed-app-78b8c5fd96-b4rb6 to minikube


Name:             probed-app-78b8c5fd96-v697t
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Fri, 05 Dec 2025 11:25:55 +0100
Labels:           app=probed-app
                  pod-template-hash=78b8c5fd96
Annotations:      <none>
Status:           Pending
IP:
IPs:              <none>
Controlled By:    ReplicaSet/probed-app-78b8c5fd96
Containers:
  nginx:
    Container ID:
    Image:          nginx
    Image ID:
    Port:           <none>
    Host Port:      <none>
    State:          Waiting
      Reason:       ContainerCreating
    Ready:          False
    Restart Count:  0
    Limits:
      cpu:     200m
      memory:  256Mi
    Requests:
      cpu:        100m
      memory:     128Mi
    Liveness:     http-get http://:80/ delay=5s timeout=1s period=10s #success=1 #failure=3
    Readiness:    http-get http://:80/ delay=3s timeout=1s period=10s #success=1 #failure=3
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-zpgdl (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   False
  Initialized                 True
  Ready                       False
  ContainersReady             False
  PodScheduled                True
Volumes:
  kube-api-access-zpgdl:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  0s    default-scheduler  Successfully assigned default/probed-app-78b8c5fd96-v697t to minikube

Comportement actuel: 
Le déploiement probed-app est créé. La description d'un pod confirme la présence des sondes liveness et readiness. 


TP5 Multi-containers

Contexte: 
Déployement d'un pod multiconteneur avec un pod "sidecar" en plus du pod d'application "app". Le pod sidecar est chargé de suivre les logs du conteneur applicatif via un volume partagé. 

Comportement attendu: 
Le pod sidecar-pod est créé. Les logs sont alors disponibles et permettent de suivre le démarrage normal de nginx. 

$ k apply -f pod-sidecar.yaml && k logs sidecar-pod -c logger
pod/sidecar-pod created
Error from server (BadRequest): container "logger" in pod "sidecar-pod" is waiting to start: ContainerCreating


$ k logs sidecar-pod -c logger


$ k logs sidecar-pod 
Defaulted container "app" out of: app, logger
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up


$ k logs sidecar-pod -c logger

$ kubectl exec sidecar-pod -c app -- cat /var/log/nginx/app.log
Fri Dec  5 10:38:51 UTC 2025 :: log
Fri Dec  5 10:38:56 UTC 2025 :: log
Fri Dec  5 10:39:01 UTC 2025 :: log
Fri Dec  5 10:39:06 UTC 2025 :: log
Fri Dec  5 10:39:11 UTC 2025 :: log
Fri Dec  5 10:39:16 UTC 2025 :: log

Comportement actuel : 
Le pod est créé. Cependant la première tentative de récupération des logs tombe en échec car le pod était encore dans l'état containerCreating. La commande n'affiche pas de logs "k logs sidecar-pod -c logger". Cependant les logs sont bien accessible lorsque la commande est effectuée directement sur le conteneur. 

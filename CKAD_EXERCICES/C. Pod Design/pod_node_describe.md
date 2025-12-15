Name:             pod-node
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube-m02/192.168.49.3
Start Time:       Mon, 15 Dec 2025 11:36:17 +0100
Labels:           run=pod-node
Annotations:      <none>
Status:           Running
IP:               
IPs:              <none>
Containers:
  pod-node:
    Container ID:   docker://519ba918c2f9936f4d3fdedc2c18f5a6373aa8947c55f8e2d68ece0bfeabab60
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:fb01117203ff38c2f9af91db1a7409459182a37c87cced5cb442d1d8fcc66d19
    Port:           <none>
    Host Port:      <none>
    State:          Waiting
      Reason:       CrashLoopBackOff
    Last State:     Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Mon, 15 Dec 2025 11:39:13 +0100
      Finished:     Mon, 15 Dec 2025 11:39:13 +0100
    Ready:          False
    Restart Count:  6
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-9ncr2 (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   False 
  Initialized                 True 
  Ready                       False 
  ContainersReady             False 
  PodScheduled                True 
Volumes:
  kube-api-access-9ncr2:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              test=toto
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason                  Age                    From               Message
  ----     ------                  ----                   ----               -------
  Normal   Scheduled               5m24s                  default-scheduler  Successfully assigned default/pod-node to minikube-m02
  Normal   Pulled                  5m21s                  kubelet            Successfully pulled image "nginx" in 2.266s (2.266s including waiting). Image size: 151914114 bytes.
  Normal   Pulled                  5m18s                  kubelet            Successfully pulled image "nginx" in 2.285s (2.285s including waiting). Image size: 151914114 bytes.
  Normal   Pulling                 5m17s (x3 over 5m23s)  kubelet            Pulling image "nginx"
  Normal   Created                 5m15s (x3 over 5m21s)  kubelet            Created container: pod-node
  Normal   Started                 5m15s (x3 over 5m21s)  kubelet            Started container pod-node
  Normal   Killing                 5m15s (x3 over 5m21s)  kubelet            Stopping container pod-node
  Normal   Pulled                  5m15s                  kubelet            Successfully pulled image "nginx" in 2.115s (2.115s including waiting). Image size: 151914114 bytes.
  Warning  FailedCreatePodSandBox  4m57s                  kubelet            Failed to create pod sandbox: rpc error: code = Unknown desc = failed to create a sandbox for pod "pod-node": Error response from daemon: Conflict. The container name "/k8s_POD_pod-node_default_ce26b447-f4dd-4317-8c69-be43576a2f2f_19" is already in use by container "4ec3286eb2b813b8a0fa61c4df0bad83c4181bcf520cd48c7c4bc21d9b893038". You have to remove (or rename) that container to be able to reuse that name.
  Normal   SandboxChanged          45s (x260 over 5m21s)  kubelet            Pod sandbox changed, it will be killed and re-created.
  Warning  BackOff                 36s (x253 over 5m14s)  kubelet            Back-off restarting failed container pod-node in pod pod-node_default(ce26b447-f4dd-4317-8c69-be43576a2f2f)

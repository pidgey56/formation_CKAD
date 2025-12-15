# Évaluation CKAD : Core Concepts & Multi-Containers
## Évaluation pratique CKAD (23% examen). Temps : 45 min. Minikube/Kind requis. Note /100 (Core : 60 pts ; Multi : 40 pts). Soumettez YAML + sorties vérifs. Docs Kubernetes.io seulement.
 
## Core Concepts (60 pts)
### Pod simple (10 pts)
Créez pod nginx-pod (image nginx:alpine, port 80, restartPolicy: Never).
 

$ k run nginx-pod --image=alpine:latest --port=80 --restart=Never


### Namespace & Labels (15 pts)
- Créez NS eval-core.
 
$ k create ns eval-core

- Pod app1 (busybox, sleep 3600, labels env=prod,tier=frontend).
 
k run app1 --image=busybox --labels='env=prod,tier=frontend' --command -- sh -c 'sleep 3600'

- Annotation owner=apprenant.
 
k annotate pod -l env=prod owner=apprenant
 
### Resources Limits/Requests (20 pts)
Pod resource-test (busybox sleep 3600) :
 
- requests : CPU 100m, memory 64Mi.

- limits : CPU 200m, memory 128Mi.
 
k apply -f resource_test.yaml 

### ResourceQuota (15 pts)
NS eval-core : quota eval-quota (2 pods max, 1 CPU, 1Gi memory). Créez 3 pods → 3e échoue.
 
k create quota eval-quota -n eval-core --hard="pods=2,cpu=1,memory=1Gi"
 
## Multi-Container Pods (40 pts)
### Pod multi-containers basique (15 pts)
Pod multi-busy :
 
- busy1 : echo hello > /tmp/hello.txt; sleep 3600.
 
- busy2 : sleep 3600. Volume emptyDir /tmp.
 
k apply -f multi_busy.yaml

### InitContainer + Volume partagé (25 pts)
Pod web-init :
 
- initContainer init (busybox) : echo "Test CKAD $(date)" > /work-dir/index.html.
 
- Container nginx (nginx:alpine, port 80).
 
- Volume emptyDir : /work-dir (init), /usr/share/nginx/html (nginx).

#### Command: 
k apply -f web-init.yaml

#### Verification 
k exec -ti web-init -- sh 

cd /usr/share/nginx/html

/usr/share/nginx/html # cat index.html
Test CKAD Thu Dec 11 10:43:32 UTC 2025
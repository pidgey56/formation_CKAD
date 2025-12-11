Contexte:

Tentative d'apporter des modifications pour l'exercice deployment probes au fichier yaml après un déploiement. 

Actions (étape par étape)

    - récuperer le fichier deployment-probes.yaml
    - executer la commande :    
        k apply -f CKAD_Semaine1/deployment-probes.yaml && k describe pod
    - modifier le fichier deployment-probes.yaml 
        replicas: 2 ==> replicas: 5
    - re-éxecuter la commande :
        k apply -f CKAD_Semaine1/deployment-probes.yaml && k describe pod

Comportement attendu:

    Augmentation du nombre de réplicas 
Comportement actuel:
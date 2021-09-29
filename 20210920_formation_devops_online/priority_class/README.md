
## Définition d'une priorité de pod
En général, les pods en attente ayant une priorité plus élevée sont planifiés avant les pods avec une priorité plus faible. Si vous ne disposez plus de ressources dans vos noeuds worker, le *planificateur* de Kubernetes peut préempter (retirer) des pods pour libérer les ressources nécessaires pour permettre la planification des pods de priorité plus élevée

### PriorityClass est non-namespaced:
Un PriorityClass est un objet *sans espace de noms* qui définit un mappage à partir d’un nom de classe de priorité à la valeur entière de la priorité,   
exp:  
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: normal
value: 1000    *ça peut y aller jusqu'a 32 chiffre 10000...............0(32)*
globalDefault: false
description: "This priority class should be used for Normal priority service pods only. "

### Utilisation de Quota + priorityclass:
Un niveau de quota bestEffot + priorityClass faible: tué et re-créé    
Un niveau de quota bestEffot + priorityClass hight: va voir dans les autres classes les pods faibles pour les tuer puis les re-créer à nouveau  
PriorityClass et les quotas fonctionnent ensemble 
Si nous spécifions une priorityClass dans un deploiment, notre pod sera créé dans le quotas qui correspondent à la classe prioritaire.  



### Command:
kubectl get PriorityClass
NAME                      VALUE        GLOBAL-DEFAULT   AGE
low                       1000         false            4m4s
normal                    10000        false            4m4s
premium                   1000000      false            4m4s



policies" sur les limits et requests :
Pour s’assurer d’une exploitation optimale et contrôlée du nœud, ceci peut se faire à l’aide de RessourceQuota au niveau namespace 
ainsi qu'avec les LimitRanges au niveau container/pod.


Requests :
C’est la quantité de ressource (cpu ou mémoire) demandée par un container.
Les Requests sont évalués au moment du scheduling, par conséquent la somme de toutes les requests de mémoire ne peut 
pas dépasser la mémoire totale allouable (allocatable) du nœud.
En ce qui concerne le cpu, la somme des requests ne peut pas dépasser la taille du cpu du nœud.

### Limits:
C’est le seuil maximal de ressources consommées que peut atteindre un pod.
C’est donc une "hard limit" qui influence le cgroup.
La valeur de limits est considérée au runtime par le Kubelet. 

### Dépassement de capaicité:
Un container/pod qui dépasse ses limits de mémoire sera automatiquement tué, il peut être redémarré ou pas par la suite selon 
sa restartPolicy. Si on dépasse les limits de CPU alors le pod va être "throttle", étrangler.

### Scheduler
Le scheduler choisi le nœud le plus adéquat pour le pod en fonction de ses requests.
Ainsi la quantité de ressource demandée sera allouée au container/pod, il s’agit donc d’une "soft limit".

#### Note:
 Si on ne spécifie pas la valeur de limits, alors elle est considérée comme infinie (unbounded).

### Niveau de quota:
 Il existe troix niveaux de quots qui peuvent être définis au niveau du pod:
 sont classés en: bestEffort, burstable et guaranteed ""(BestEffort < Burstable < Guaranteed)""

 Différence:
 _BestEffort_ < __Burstable__ < __Guaranteed__)
 Burstable requests<limits

 resources:
          requests:
            memory: "500Mi"
          limits:
            memory: "2Gi"
          limits:
            memory: "2Gi"
            cpu : "100m"

 Guaranteed requests=limits
limits:
            memory: "2Gi"
            cpu: "100m"


Overcommitment
Si la somme totale des limits de tous les pods est supérieure à la mémoire allouable du nœud, alors on dit que le nœud est overcommitted.
L'overcommitment part de l’hypothèse que tous les pods ne vont pas atteindre leurs limites en même temps.


Nœud surchargé
Cela qui peut malheureusement arriver (par exemple quand les limits de mémoire sont trop optimistes ou que tous 
les pods consomment beaucoup plus que leurs requests en même temps.

Eviction:
En cas de surcharge du nœud Kubernetes peut faire recours à l'éviction qui est un mécanisme de défense ( paramétré au niveau du kubelet ). 
Il s’agit de "descheduler" certains pods par ordre de priorité selon le niveau de QoS, 
Il s’agit d’une hard limit au niveau du nœud sur la mémoire restante:
(--eviction-hard="memory.available<200Mi")


Libération de mémoire:
Dans cet exemple, en cas de surcharge du nœud si la mémoire totale restante est inférieure à 200Mi,
l’éviction sera enclenchée et les pods qui ont les QoS les plus basses seront évincés en premier pour libérer les ressources aux pods de QoS plus élevés.








 référence: https://blog.wescale.fr/2018/09/14/k8s-gestion-de-ressources/
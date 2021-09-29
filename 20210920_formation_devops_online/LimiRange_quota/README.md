

## LimitRange, QoS et éviction
On distingue deux types de ressources :
### Types de ressources
#### Compressibles :
Ce sont les ressources qui sont *inépuisables* du moment qu’on a le temps pour attendre qu’ils soient de nouveau disponibles (ex : *CPU, Réseau*). En cas de surcharge, on peut toujours avoir un accès limité à ces ressources (*Throttling*), mais avec un risque de latence et de famine.

#### Non compressibles :
Ce sont les ressources limitées, si toutes les ressources disponibles sont consommées l’application ne pourra plus en avoir plus (ex : *Mémoire, Disque..*), En cas de surcharge, certains processus peuvent être tués (*kill, OOM*), si le système a besoin de libérer des ressources.

### policies " sur les limits et requests :
Pour s’assurer d’une exploitation optimale et contrôlée du nœud, ceci peut se faire à l’aide de RessourceQuota au niveau namespace ainsi qu'avec les LimitRanges au niveau container/pod.


### Requests:
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
 __BestEffort__ < __Burstable__ < __Guaranteed__)
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


### Application de quota:
Des quotas par namespace sur le total de limits et de requests autorisés. L ’api-server renvoie une erreur "403 Forbidden" si le quota est dépassé.

### Overcommitment:
Si la somme totale des limits de tous les pods est supérieure à la mémoire allouable du nœud, alors on dit que le nœud est overcommitted.
L'overcommitment part de l’hypothèse que tous les pods ne vont pas atteindre leurs limites en même temps.


### Nœud surchargé:
Cela qui peut malheureusement arriver (par exemple quand les limits de mémoire sont trop optimistes ou que tous 
les pods consomment beaucoup plus que leurs requests en même temps.

### Eviction:
En cas de surcharge du nœud Kubernetes peut faire recours à l'éviction qui est un mécanisme de défense ( paramétré au niveau du kubelet ). 
Il s’agit de "descheduler" certains pods par ordre de priorité selon le niveau de QoS, 
Il s’agit d’une hard limit au niveau du nœud sur la mémoire restante:
(--eviction-hard="memory.available<200Mi")


### Libération de mémoire:
Dans cet exemple, en cas de surcharge du nœud si la mémoire totale restante est inférieure à 200Mi,
l’éviction sera enclenchée et les pods qui ont les QoS les plus basses seront évincés en premier pour libérer les ressources aux pods de QoS plus élevés.

### RessourceQuota Scope
Les RessourceQuota peuvent être aussi spécifiées par scope de pod, on pourrait vouloir par exemple associer un quota qui ne s’applique que sur les pods NotBestEffort ou Terminating etc..
spec:
  hard:
    pods: "3"
  scopes:
  - NotBestEffort

### Edit the hard eviction thresholds of worker nodes
vim /usr/lib/systemd/system/kubelet.service  
--evictionhard=  
memory.available<500Mi,nodefs.available<5Gi,imagefs.available<5Gi  
--system-reserved=memory=1.5Gi  

Run the following commands to enable the new thresholds:  
systemctl daemon-reload  
systemctl restart kubelet  

## Best practices  
Il faut toujours définir des limits et des requests pour les pods les plus critiques:
Quand la limite n’est pas définie, Kubernetes considère que toutes les ressources du nœud peuvent être consommées au runtime.

Gérer la stabilité des déploiements et des nœuds en se basant sur les classes  de QoS:

Éviter la création de pod BestEffort si l’application est sensible aux restarts/kill.

Les pods critiques tels que les bases de données et les services statefull doivent être Guaranteed si on veut minimiser la latence CPU et éviter qu’ils soient tués en cas de surcharge de mémoire.

Burstable c’est pour les applications les plus communes, mais qu’on veut contrôler leur consommation de ressources en mettant des limits  (Un serveur web par exemple).

Un pod avec une request basse aura plus de facilité à être schedulé.

Utiliser les LimitRange, pour être sûr d’avoir toujours des requests et des limits bien définis, on associant des valeurs par défaut et en imposant des bornes.

Protéger le nœud et les processus critiques de Kubernetes, en ajustant le *threshold d'éviction*.

Imposer la définition de limits (et/ou) requests et séparer les environnements en associant *RessourceQuotas* par *namespace* (ex : un quota limité sur le namespace de l’environnement DEV) .

*Monitorer* pour bien estimer la consommation de ressources et définir les RessourceQuota et les LimitRange en fonction de cela pour une meilleure exploitation du nœud.

 référence: https://blog.wescale.fr/2018/09/14/k8s-gestion-de-ressources/
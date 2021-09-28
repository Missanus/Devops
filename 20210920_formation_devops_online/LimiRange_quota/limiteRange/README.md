
Les limites:
Les limites sont définies dans .spec.containers[*].ressources d’un pod.
Elles sont donc spécifiées pour chaque conteneur au sein du pod.
Elles permettent de contrôler l’utilisation CPU, de stockage éphémère, et de mémoire d’une application, pour donner un garde fou à celle-ci.
Il s’agit d’une pratique incontournable, surtout pour les applications potentiellement mal codées ou naturellement gourmandes en RAM (Java, on vous voit).

scheduler:
Pour rappel, le scheduler (qui décide quel pod va se retrouver sur quel node) se base seulement sur les requests et non pas les limits. 
Également, il n’a pas de bille pour savoir quel est l’usage RAM réel d’un node à un moment. 

Comment choisir la RAM/memory et les CPU:
il n’a pas de bille pour savoir quel est l’usage RAM réel d’un node à un moment donné. Tout ce qu’on peux faire, c’est de s’appuyer sur les estimations des développeurs dont le boulot est alors de définir une valeur “nominale” de consommation de mémoire vive (requests), ainsi qu’une valeur “max” (limits).

Manque de RAM:
Il y a de chances que une ou plusieurs de ces applications en viennent à consommer toute la RAM d’un node à un moment donné.
Que se passera-t-il à ce moment-là ? Et bien par défaut, comme sur n’importe quelle machine Linux qui n’a pas d’espace de swap (prohibé avec Kubernetes), ça explose :)

RAM Available:
Une fois que la mémoire disponible est basse sur une machine, le Node Controller de Kubernetes s’en rendra compte seulement si un eviction signal est reçu. Cela veut dire qu’il faut qu’un seuil soit dépassé, et ce seuil est celui des Evictions définis par la Kubelet.

Par défaut, la configuration Kubelet est la suivante :

evictionHard:
  memory.available: 100Mi

Cela veut dire qu’un eviction signal sera envoyé par la Kubelet seulement si la mémoire vive disponible descend sous les 100Mi.

Calculer la Mémoire RAM
En prenant la valeur de capacité mémoire du node (elle même étant la mémoire totale moins les valeurs de systemReserved, kubeReserved et la valeur d’evictionHard), en y retranchant la conso mémoire (le “working-set”) des applications tournant sur le node.

Eviction:
Node-pressure eviction is the process by which the kubelet proactively terminates pods to reclaim resources on nodes. The kubelet monitors resources like CPU, memory, disk space, and filesystem inodes on your cluster's nodes.   


Utilisation des ressource:
kubectl describe node $HOSTNAME (kubemaster or kubenode1 ...)
In which node the ressources (pods for exemple)  are running: kubectl get pods -o wide -n dev-lr




source: https://guillaume.fenollar.fr/blog/kubernetes-conditions-eviction-memory-pressure/
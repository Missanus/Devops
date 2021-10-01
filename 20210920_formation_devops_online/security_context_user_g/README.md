

### 1. runAsNonRoot
Même si un conteneur utilise des espaces de noms et des cgroups pour limiter son ou ses processus, il suffit d’une mauvaise configuration dans ses paramètres de déploiement pour accorder à ces processus l’accès aux ressources sur l’hôte. Si ce processus s’exécute en tant que root, il dispose du même accès que le compte racine hôte à ces ressources. En outre, si d’autres paramètres de pod ou de conteneur sont utilisés pour réduire les contraintes (c’est-à-dire procMount ou capacités),le fait d’avoir un UID racine aggrave les risques de toute exploitation de ceux-ci. À moins que vous n’ayez une très bonne raison,  
vous ne devez jamais exécuter un conteneur en tant que root.

##### Option 1 : Utiliser l’utilisateur fourni dans l’image de base
FROM node:slim
COPY --chown=node . /home/node/app/   # <--- Copy app into the home directory with right ownership  
USER 1000                             # <--- Switch active user to “node” (by UID)  
WORKDIR /home/node/app                # <--- Switch current directory to app  
ENTRYPOINT ["npm", "start"]           # <--- This will now exec as the “node” user instead of root  


##### Option 2 : L’image de base ne fournit aucun utilisateur:
FROM node:slim
RUN useradd somebody -u 10001 --create-home --user-group  # <--- Create a user  
COPY --chown=somebody . /home/somebody/app/  
USER 10001  
WORKDIR /home/somebody/app    
ENTRYPOINT ["npm", "start"]  

### 2. runAsUser / runAsGroup
Les images de conteneur peuvent avoir un utilisateur et/ou un groupe spécifique configuré pour que le processus s’exécute en tant que
...  
spec:  
  containers:  
  - name: web
    image: mycorp/webapp:1.2.3
  securityContext:
    runAsNonRoot: true
    runAsUser: 10001
...
...


vagrant@kubemaster:~/devops/Devops/20210920_formation_devops_online/security_context_user_g$ k exec -ti --tty busy-pod bysunode1 -- sh
Defaulted container "busynode1" out of: busynode1, busynode2
$
$ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND  
1000         1  0.0  0.0   4336   740 ?        Ss   10:35   0:00 /bin/sh -c sleep 600  ##  
1000         6  0.0  0.0   4236   692 ?        S    10:35   0:00 sleep 600  
1000        14  0.0  0.0   4336   712 pts/0    Ss   10:37   0:00 sh  
1000        19  0.0  0.1  17500  2096 pts/0    R+   10:37   0:00 ps aux  

Affichez les fonctionnalités du processus 1 :
bitmap for process 1:
cd /proc/1
cat status

...
CapPrm:	00000000a80425fb  
CapEff:	00000000a80425fb  
...




source
https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
https://snyk.io/blog/10-kubernetes-security-context-settings-you-should-understand/
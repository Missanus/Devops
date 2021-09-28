L'objctif de ce projet est de créer un un nginx-reverse-proxy attaché à un service et un ingress

on'est parti sur une architecture qu'est basée sur 3 conteneur nginx, un des conteneur on lui expose son port (80) les deux autres contenuers Non.
Le but de ce tp est que le 1 nginx sert comme un lb, c'est à lui de mapper et acheminer les requêtes vers les deux autres containers, tout déployer dans un seul POD.
La connexion entre ses 3 conteneurs se fait à partir d'un endpoint définit dans le serviceIP
Note:
on a créé un nom de domaine qui pour indentifier notre unique endpoint ( à renseigner au niveau /etc/hosts) 
In this projetct, we create à container nginx LB another to reach other nginx container inside the same POD but this latter without targetPort for this container, we use the dsn resolution  


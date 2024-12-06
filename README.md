# mauvaisePratiques

Analyse des mauvaises pratiques

Nombre de réplicas insuffisant : Avec un seul replica, l'application n'est pas tolérante aux pannes.

Image tag latest : L'utilisation de latest rend le déploiement imprévisible et difficile à reproduire.

Pas de limites ou de demandes de ressources : Cela peut provoquer des conflits sur les ressources du cluster.

Exécution en mode privilégié : Donne à l'application un accès complet à l'hôte, exposant tout le système.

Autorisation d'escalade des privilèges : Une faille dans l'application pourrait permettre à un attaquant de prendre le contrôle du conteneur et de l'hôte.

Variables d'environnement sensibles : Les secrets sont exposés en texte clair, ce qui est une faille de sécurité.

Montage de volumes sensibles de l'hôte : Monter le répertoire racine de l'hôte met en danger l'intégrité et la sécurité de l'infrastructure.

Politique de redémarrage Always : Si l'application plante à cause d'une mauvaise configuration, elle redémarrera indéfiniment, masquant potentiellement les problèmes.

Pourquoi hostIPC: true est une mauvaise pratique ?

Risque de fuite d'informations : Avec hostIPC: true, le conteneur peut accéder à des segments de mémoire partagée de l'hôte ou d'autres conteneurs.
Escalade des privilèges : Si un attaquant compromet le conteneur, il pourrait exploiter cette option pour accéder à des processus sensibles sur l'hôte.
Sécurité isolée compromise : Une des promesses fondamentales des conteneurs est leur isolation. Cette option la brise.



En résumé

Ce manifeste YAML illustre exactement ce qu'il ne faut jamais faire dans un environnement Kubernetes. En production, il est crucial de suivre les bonnes 
pratiques, telles que définir des limites de ressources, éviter les privilèges inutiles et sécuriser les secrets via des outils comme Secrets.

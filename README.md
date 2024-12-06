# mauvaisePratiques

# Mauvaises Pratiques de Sécurité dans Kubernetes

## 1. Mauvaises pratiques de configuration des conteneurs
- **Exécution en tant qu'utilisateur root** : Ne pas spécifier d'utilisateur non privilégié dans le champ `securityContext.runAsUser`.
- **Absence de `readOnlyRootFilesystem`** : Permettre à un conteneur d'écrire sur le système de fichiers racine augmente le risque d'altération.
- **Volume `emptyDir` utilisé sans précaution** : Les données stockées dans ces volumes peuvent être effacées lors d'un redémarrage.
- **Pas de `liveness` ou `readiness` probes** : Omettre ces sondes peut masquer des problèmes ou laisser tourner des conteneurs défaillants.
- **Ports exposés inutiles** : Déclaration de ports non nécessaires ou accessibles au public.

## 2. Problèmes liés aux privilèges
- **Privilèges inutiles** : Exécution des conteneurs avec `privileged: true` ou autorisation d'accès à des fonctionnalités dangereuses (e.g., `capabilities.add` avec des capacités comme `SYS_ADMIN`).
- **Autorisation de monter des volumes sensibles** : Utilisation de volumes `hostPath` pointant vers des répertoires critiques (`/`, `/var`, `/etc`).
- **`allowPrivilegeEscalation: true`** : Permet à un processus dans un conteneur d'augmenter ses privilèges, ce qui est souvent inutile.

## 3. Manque de gestion des ressources
- **Absence de `resources.requests` et `resources.limits`** : Cela peut entraîner une concurrence incontrôlée pour les ressources, provoquant des pannes dans le cluster.
- **Pas de quotas de ressources au niveau du namespace** : Les utilisateurs ou applications peuvent consommer toutes les ressources disponibles.

## 4. Utilisation de configurations non sécurisées
- **Exposition de secrets en texte clair** : Inclure des informations sensibles dans des fichiers YAML ou en tant que variables d'environnement non chiffrées.
- **Utilisation directe des `hostNetwork` et `hostPort`** : Cela expose les applications directement sur le réseau de l'hôte.
- **Manque de NetworkPolicies** : Absence de contrôle du trafic réseau entre les pods, exposant tout à tout.
- **Utilisation de `hostPID` ou `hostIPC`** : Cela partage l'espace de PID ou IPC avec l'hôte, compromettant l'isolation.

## 5. Gestion inadéquate des images
- **Images non scannées** : Ne pas utiliser d'analyse de sécurité pour détecter les vulnérabilités dans les images de conteneur.
- **Utilisation d’images non signées ou non vérifiées** : Augmente le risque d'introduction de logiciels malveillants.
- **Images provenant de dépôts publics inconnus** : Les sources non fiables peuvent contenir du code malveillant.

## 6. Erreurs de configuration des RBAC (Role-Based Access Control)
- **Attribution excessive de privilèges** : Utilisation de rôles ou clusters avec des droits trop larges (`cluster-admin` ou accès global à des ressources critiques).
- **Absence de RBAC** : Permettre aux utilisateurs ou aux pods d’accéder librement à toutes les ressources.
- **Pas de `serviceAccount` dédié** : Utiliser le compte de service par défaut, souvent sur-privilégié.
- **Accès global aux secrets** : Attribution de permissions de lecture à tous les secrets du namespace.

## 7. Exposition non contrôlée des services
- **Utilisation de `NodePort` sans contrôle d'accès** : Expose les services directement sur les nœuds, accessible depuis Internet.
- **Absence d'authentification pour les services publics** : Permet un accès non sécurisé à des APIs ou applications sensibles.
- **ClusterIP accessible à tous les pods** : Absence de filtrage du trafic interne via des **NetworkPolicies**.

## 8. Absence de journalisation et surveillance
- **Pas de collecte de journaux** : Rend difficile la détection ou l’investigation des incidents.
- **Pas de monitoring des ressources** : Absence d'outils comme Prometheus pour surveiller l'utilisation des ressources ou détecter des anomalies.

## 9. Absence de règles de PodSecurity (PodSecurityPolicy/AdmissionControllers)
- **Pas de politique pour limiter les actions des pods** : Par exemple, permettre à tous les pods d’utiliser `privileged: true`.
- **Absence de validation des manifestes** : Pas d'utilisation d'admission controllers comme `Gatekeeper` ou `Kyverno` pour imposer des règles de sécurité.

## 10. Manque de segmentation des namespaces
- **Tout dans le namespace `default`** : Les namespaces permettent d'isoler les applications et leurs ressources. Ne pas les utiliser complique la gestion.
- **Pas d'isolation entre namespaces sensibles** : Par exemple, les applications publiques et internes partageant le même namespace.

---

## Conclusion
Ces mauvaises pratiques compromettent la sécurité des déploiements Kubernetes. Adopter les bonnes pratiques associées est essentiel pour garantir une infrastructure robuste et sûre.



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

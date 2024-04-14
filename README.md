# Projet CICD

# Projet : Mise en place du CI/CD dans un environnement Kubernetes pour une application de microservices

## *Louis Brochard et Olivia MOREAU*

1. **Dockerisation**
    - Nous avons créé des fichiers Dockerfile pour chaque service puis un docker-compose.yaml qui permet de lancer tous les conteneurs. Pour lancer ces conteneurs, il suffit de taper la commande suivante : `docker compose up -d` .
    - L’étape suivante est de déployer les images docker sur le Docker Hub. Pour cela, il faut :
        - D’abord créer un repository avec le nom du service.
        - Puis, build les images en allant dans le dossier consacré au service à build, puis faire la commande : `docker build -t front-end-react .` pour chaque service, en changeant le nom du service.
        - Ensuite, taguer l’image avec le lien du repository : `docker tag front-end-react your-docker-profile/front-end-react:0.1.0`
        - Enfin, pusher l’image : `docker push your-docker-profile/front-end-react:0.1.0`

1. **Mise en place du Pipeline CI**
    - Nous avons choisi l’outil Github Actions pour mettre le pipeline en place. il se trouve dans le  dossier `.github/workflow/` et est nommé  `build-images.yml` .
    - Dans ce fichier, on retrouve les différentes étapes du workflow pour déployer les images des services dès qu’un push ou une pull request est fait sur la branche `main` :
        - Une connexion est établie avec DockerHub à l’aide de secrets Github
        - Chaque service est ensuite automatiquement build, tag puis push sur Dockerhub
    
2. **Versioning**
    - De même que pour la partie 2, nous avons choisi Github Actions pour mettre en place le pipeline de versioning. Il se trouve également dans le dossier `.github/workflow/` et est nommé  `new-branch.yml` .
    - On peut trouver dans ce fichier les étapes du workflow pour construire une image lorsqu’une branche est créé avec un nom qui correspond à une version d’application (ex : 1.4.5) :
        - Lorsqu’une branche est créée, on vérifie que son nom correspond aux critères
        - On extrait ensuite le nom de cette branche
        - On établie une connexion avec DockerHub à l’aide de secrets Github
        - Puis chaque service est ensuite automatiquement build, tag en utilisant comme version le nom de la branche, puis push sur Dockerhub
    
3. **Déploiement dans Kubernetes**
    - Dans le repository [micro-app-deploy](https://github.com/Liv44/microapp-deploy), nous avons créé un dossier pour chaque service avec dedans un fichier pour le déploiement et un pour le service.
    - Pour déployer sur le cluster Kubernetes voici les commandes à faire :
        - `minikube start` pour lancer le cluster
        - `kubectl apply -f deployment.yml` pour appliquer le déploiement prévu
        - `kubectl apply -f service.yml` pour lancer le service
        - On peut vérifier que les services sont bien lancés avec `kubectl get pods`
    
4. **Déploiement avec ArgoCD :**
    
    **Malheureusement nous n’avons pas pu terminer le déploiement avec ArgoCD, et il n’est donc pas opérationnel pour le moment. Voici les étapes que nous aurions dû faire pour la mise en place.**
    
    - Créer le namespace : `kubectl create namespace argocd`
    - Aller chercher le manifest et l’installer `kubectl apply -n argocd -f [https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml](https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml)`
    - Accéder à l’application : `kubectl port-forward svc/argocd-server -n argocd 8080:443` (source : [https://www.digitalocean.com/community/tutorials/how-to-deploy-to-kubernetes-using-argo-cd-and-gitops](https://www.digitalocean.com/community/tutorials/how-to-deploy-to-kubernetes-using-argo-cd-and-gitops))
    - Il faut ensuite aller chercher le secret avec cette commande : `kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`(source : [https://stackoverflow.com/questions/68297354/what-is-the-default-password-of-argocd](https://stackoverflow.com/questions/68297354/what-is-the-default-password-of-argocd))
    - Puis ensuite, il faut se login avec `argocd login localhost:8080` (source : [https://www.digitalocean.com/community/tutorials/how-to-deploy-to-kubernetes-using-argo-cd-and-gitops](https://www.digitalocean.com/community/tutorials/how-to-deploy-to-kubernetes-using-argo-cd-and-gitops))

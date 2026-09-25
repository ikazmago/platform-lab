# Projet 1 — Fondations Kubernetes (déploiement)

## Objectif

Comprendre et manipuler les objets Kubernetes essentiels en déployant une application simple (NGINX) et en automatisant ce déploiement avec Ansible.

## Concepts travaillés

- Architecture cluster : control-plane, workers, API server.
- Pods, Deployments, ReplicaSets.
- Namespaces, labels et selectors.
- Services : ClusterIP, NodePort.
- Requests/limits.
- Probes : readiness, liveness.
- Rolling updates.
- Utilisation de `kubectl` et du module `kubernetes.core.k8s` d’Ansible.

## Architecture de l’application

- Namespace : `demo`.
- Deployment : `nginx-demo` (2 replicas).
  - Image : `nginx:1.28`.
  - Probes de readiness et liveness.
  - Requests/limits CPU/mémoire.
  - `nodeSelector` pour cibler les workers.
- Service : `nginx-demo-service` (type `NodePort`).
  - Expose le port 80 des pods sur un port élevé (ex. 32151) sur chaque nœud.

## Fichiers de ce projet

```text
.
├── README.md
├── kubernetes/
│   ├── namespaces/
│   │   └── demo.yml
│   └── apps/
│       └── nginx/
│           ├── deployment.yml
│           └── service.yml
└── playbooks/
    └── deploy-nginx.yml
```

- `kubernetes/namespaces/demo.yml` : définition du namespace `demo`.
- `kubernetes/apps/nginx/deployment.yml` : Deployment NGINX.
- `kubernetes/apps/nginx/service.yml` : Service NodePort.
- `playbooks/deploy-nginx.yml` : playbook Ansible qui applique ces manifests.

## Commandes principales

### Déployer avec kubectl (manuel)

Depuis la racine `~/platform-lab` :

```bash
kubectl apply -f kubernetes/namespaces/demo.yml
kubectl apply -f kubernetes/apps/nginx/
```

### Vérifier l’état

```bash
kubectl get namespaces
kubectl get all -n demo
kubectl get pods -n demo -o wide
kubectl get service -n demo
kubectl rollout status deployment/nginx-demo -n demo
```

### Accéder à l’application

```bash
NODEPORT=$(kubectl get service nginx-demo-service -n demo -o jsonpath='{.spec.ports.nodePort}')
curl http://192.168.56.11:$NODEPORT
```

### Déployer avec Ansible

Depuis `~/platform-lab/project-1-kubernetes-deployment` :

```bash
ansible-playbook playbooks/deploy-nginx.yml
```

Le playbook :

- Applique le namespace.
- Applique le Deployment et le Service.
- Attend que 2 replicas soient disponibles.
- Affiche le NodePort utilisé.

## Rolling update (exemple)

Mise à jour de l’image NGINX :

1. Modifier `kubernetes/apps/nginx/deployment.yml` :
   ```yaml
   image: nginx:1.28
   ```
2. Appliquer :
   ```bash
   kubectl apply -f kubernetes/apps/nginx/deployment.yml
   kubectl rollout status deployment/nginx-demo -n demo
   ```
3. Vérifier que les nouveaux pods tournent avec la nouvelle image.

## Difficultés rencontrées (et solutions)

- `spec.selector: Invalid value ... field is immutable`  
  → Le selector d’un Deployment ne peut pas être changé. Solution : supprimer le Deployment puis le recréer.
- `Failed to import the required Python library (kubernetes)` pour Ansible  
  → Installer `python3-kubernetes` via `apt` sur le poste d’administration.
- `TLS handshake timeout` avec `kubectl`  
  → Souvent temporaire ; réessayer. Vérifier la connectivité réseau vers le control-plane.

## Prochaines étapes (Phase 1)

Dans la continuité de ce projet, la Phase 1 prévoit notamment :

- Ajout de ConfigMap et Secret.
- Exercices de panne (ImagePullBackOff, CrashLoopBackOff, etc.).
- Mise en place d’un Ingress basique.
- Déploiement d’une application multi-replicas accessible en Ingress, en expliquant chaque ressource.

# platform-lab

Laboratoire personnel Kubernetes et automatisation, organisé en phases d’apprentissage.

## Organisation

Ce dépôt est structuré en deux grandes phases :

- **Phase 0 — Mise en place du lab**  
  Infrastructure de base : VM, réseau, K3s, Ansible, poste d’administration.
- **Phase 1 — Fondations Kubernetes**  
  Objets Kubernetes essentiels, déploiement d’applications, diagnostics de pannes.

Chaque phase contient un ou plusieurs projets avec leur propre documentation.

## Structure des dossiers

```text
.
├── README.md
├── project-0-infrastructure/
│   └── README.md
└── project-1-kubernetes-deployment/
    └── README.md
```

- `project-0-infrastructure/` : tout ce qui concerne l’infrastructure (Projet 0).
- `project-1-kubernetes-deployment/` : déploiement d’applications Kubernetes (Projet 1).

## Prérequis

- WSL2 avec un terminal Linux.
- VirtualBox et 3 VM (1 control-plane, 2 workers).
- `kubectl` configuré pour parler au cluster K3s.
- Ansible installé sur le poste d’administration.

## Démarrage rapide

### Vérifier le cluster

Depuis le poste d’administration (WSL) :

```bash
kubectl get nodes
```

### Déployer l’application de test (Projet 1)

```bash
cd project-1-kubernetes-deployment
ansible-playbook playbooks/deploy-nginx.yml
```

### Accéder à l’application

```bash
NODEPORT=$(kubectl get service nginx-demo-service -n demo -o jsonpath='{.spec.ports.nodePort}')
curl http://192.168.56.11:$NODEPORT
```

## Liens vers les projets

- [Projet 0 — Foundations Lab](project-0-infrastructure/README.md)
- [Projet 1 — Fondations Kubernetes (déploiement)](project-1-kubernetes-deployment/README.md)

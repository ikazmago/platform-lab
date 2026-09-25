# Projet 0 — Foundations Lab

## Objectif

Mettre en place un environnement local stable, reproductible et documenté pour tous les projets Kubernetes suivants.

## Compétences travaillées

- VirtualBox et création de VM Linux.
- Réseau NAT / Host-only.
- SSH et gestion des clés.
- WSL2 comme poste d’administration.
- Ansible de base (inventory, playbooks simples).
- Installation de K3s multi-nœuds.
- Utilisation de `kubectl` et notions de base Helm.
- Git/GitHub (dépôt propre, sans secrets).

## Architecture

- 1 VM control-plane : `cp-01` (K3s server).
- 2 VM workers : `worker-01`, `worker-02` (K3s agents).
- 1 poste d’administration : WSL2 avec `kubectl`, Ansible, fichiers de projet.

Le control-plane expose l’API Kubernetes sur le port 6443.  
Les commandes `kubectl` et les playbooks Ansible sont exécutés depuis WSL et envoient des requêtes à cette API.  
Les workloads (pods) s’exécutent sur les workers.

## Fichiers de ce projet

```text
.
├── README.md
├── ansible.cfg
├── inventory/
│   └── hosts.ini
└── playbooks/
    ├── 01-prepare-nodes.yml
    └── 02-install-k3s.yml
```

- `ansible.cfg` : configuration Ansible (inventory, options).
- `inventory/hosts.ini` : définition des groupes `k3s_server` et `k3s_agents`.
- `playbooks/01-prepare-nodes.yml` : préparation des nœuds (paquets, configuration de base).
- `playbooks/02-install-k3s.yml` : installation de K3s (server + agents).

## Commandes principales

### Vérifier la connectivité Ansible

Depuis `~/platform-lab/project-0-infrastructure` :

```bash
ansible all -m ping
```

### Vérifier l’état de K3s

```bash
ansible k3s_server -b -m shell -a 'systemctl is-active k3s'
```

### Vérifier le cluster avec kubectl

Depuis WSL (peu importe le dossier) :

```bash
kubectl get nodes
kubectl get nodes --show-labels
```

## Validation de la Phase 0

La Phase 0 est considérée comme terminée quand :

- Les trois nœuds sont en état `Ready` :
  ```bash
  kubectl get nodes
  ```
- `kubectl get nodes` fonctionne depuis le poste d’administration (WSL).
- Un déploiement NGINX de test avec 2 replicas fonctionne (voir Projet 1).
- Les scripts de health check de base sont disponibles (ping Ansible, `kubectl get nodes`).
- Les snapshots VirtualBox sont documentés (procédure de sauvegarde/restore).
- Le dépôt Git `platform-lab` est propre, sans secret ni kubeconfig.

## Snapshots VirtualBox (procédure)

1. Dans VirtualBox, pour chaque VM (`cp-01`, `worker-01`, `worker-02`) :
   - Menu `Machine` → `Take Snapshot`.
   - Nom : par exemple `after-k3s-install`.
   - Description : état après installation de K3s et cluster sain.
2. Pour restaurer :
   - Sélectionner la VM.
   - Menu `Machine` → `Snapshots`.
   - Choisir le snapshot → `Restore`.

## Prochaines étapes

Voir [Projet 1 — Fondations Kubernetes](../project-1-kubernetes-deployment/README.md).

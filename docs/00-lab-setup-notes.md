# Lab setup notes – Vagrant + WSL

## Architecture générale

- **Vagrant + VirtualBox** sont installés sur **Windows**.
- Les VMs du cluster (`cp-01`, `worker-01`, `worker-02`) sont gérées par Vagrant depuis **PowerShell**.
- **WSL** est utilisé pour :
  - `kubectl` (interagir avec le cluster Kubernetes).
  - `git` (gérer le dépôt `platform-lab`).
  - éditer les manifests YAML.

## Commandes typiques

### PowerShell (gestion des VMs)

Depuis le dossier contenant le `Vagrantfile` (par exemple `C:\platform-lab`) :

```powershell
cd C:\platform-lab
vagrant status
vagrant ssh cp-01
vagrant ssh worker-01
vagrant ssh worker-02
```

Dans une VM, pour créer un dossier par exemple :

```bash
sudo mkdir -p /mnt/data/demo
```

### WSL (Kubernetes + Git)

Dans WSL, depuis `~/platform-lab` :

```bash
kubectl get nodes
kubectl get pods -n demo
kubectl apply -f project-1-kubernetes-deployment/exercises/
git status
git add .
git commit -m "..."
git push
```

## Pièges à éviter

- **Ne pas mélanger** :
  - SSH direct depuis WSL vers les VMs (`ssh vagrant@192.168.56.xx`).
  - `vagrant ssh` depuis PowerShell.
- Toujours utiliser **`vagrant ssh` depuis PowerShell** pour accéder aux VMs.
- Utiliser **WSL uniquement** pour :
  - `kubectl`
  - `git`
  - l’édition des fichiers du projet.

## Pourquoi cette séparation ?

- Vagrant est installé sur Windows et gère les VMs via VirtualBox.
- WSL est un environnement Linux séparé, pratique pour les outils Kubernetes et Git.
- Cette séparation est courante et fonctionne bien, à condition de bien garder en tête quel outil tourne où.

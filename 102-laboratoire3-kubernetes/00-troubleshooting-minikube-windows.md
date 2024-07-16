1. **Démarrer Minikube avec VirtualBox sans vérification VT-x/AMD-v** :
   ```bash
   minikube start --driver=virtualbox --no-vtx-check
   ```

2. **Lister les profils Minikube existants** :
   ```bash
   minikube profile list
   ```

3. **Supprimer le profil Minikube actuel** :
   ```bash
   minikube delete --profile minikube
   ```

4. **Démarrer Minikube avec plus de ressources CPU et mémoire, et spécifier VirtualBox comme driver** :
   ```bash
   minikube start --vm-driver=virtualbox --cpus 8 --memory 16g
   ```

5. **(Facultatif) Démarrer Minikube avec les mêmes ressources tout en désactivant la vérification VT-x/AMD-v** :
   ```bash
   minikube start --vm-driver=virtualbox --cpus 8 --memory 16g --no-vtx-check
   ```

Ces commandes vous permettront de gérer et configurer Minikube selon vos besoins, en adaptant les ressources et en contournant les limitations potentielles du matériel.

```bash
minikube start --driver=virtualbox --no-vtx-check
minikube profile list
minikube delete --profile minikube
minikube start --vm-driver=virtualbox --cpus 8 --memory 16g
minikube start --vm-driver=virtualbox --cpus 8 --memory 16g --no-vtx-check
```



# how to resolve minikube start error : This computer doesn't have VT-X/AMD-v enabled. Enabling it in the BIOS is mandatory [closed]

- minikube start --driver=virtualbox --no-vtx-check
- https://stackoverflow.com/questions/70813386/how-to-resolve-minikube-start-error-this-computer-doesnt-have-vt-x-amd-v-enab
- https://stackoverflow.com/questions/72147700/deleting-minikube-cluster-so-i-can-create-a-larger-cluster-with-more-cpus


- minikube profile list
- minikube delete --profile minikube
- minikube start --vm-driver=virtualbox --cpus 8 --memory 16g
# minikube start --vm-driver=virtualbox --cpus 8 --memory 16g  --no-vtx-check
- https://stackoverflow.com/questions/70813386/how-to-resolve-minikube-start-error-this-computer-doesnt-have-vt-x-amd-v-enab



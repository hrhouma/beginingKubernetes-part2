# how to resolve minikube start error : This computer doesn't have VT-X/AMD-v enabled. Enabling it in the BIOS is mandatory [closed]

- minikube start --driver=virtualbox --no-vtx-check
- https://stackoverflow.com/questions/70813386/how-to-resolve-minikube-start-error-this-computer-doesnt-have-vt-x-amd-v-enab
- https://stackoverflow.com/questions/72147700/deleting-minikube-cluster-so-i-can-create-a-larger-cluster-with-more-cpus


- minikube profile list
- minikube delete --profile minikube
- minikube start --vm-driver=virtualbox --cpus 8 --memory 16g



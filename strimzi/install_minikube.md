install minikube


wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum localinstall epel-release-latest-7.noarch.rpm

yum -y install libvirt qemu-kvm virt-install virt-top libguestfs-tools bridge-utils
systemctl enable libvirtd

sudo usermod -a -G libvirt $(whoami)



wget https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
chmod +x minikube-linux-amd64
sudo mv minikube-linux-amd64 /usr/local/bin/minikube

curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mv kubectl  /usr/local/bin/

#create admin user
adduser admin
usermod -aG wheel admin

su to new user

# add user to libvirt group
sudo usermod -a -G libvirt $(whoami)

# adapt libvirt conf
sudo vi /etc/libvirt/libvirtd.conf

uncomment the following

        unix_sock_group = "libvirt"
        unix_sock_rw_perms = "0770"

restart 
sudo systemctl restart libvirtd.service

# start minikube 3 node cluster
minikube config set memory 8192
minikube start --nodes 3 -p multinode-kafka


## strimzi part

wget https://github.com/strimzi/strimzi-kafka-operator/releases/download/0.22.1/strimzi-0.22.1.tar.gz






# create cluster roles

kubectl create -f install/strimzi-admin


# change the namespace for cluster operator to "cluster-operator"
sed -i 's/namespace: .*/namespace: $NAMESPACE/g' install/cluster-operator/*RoleBinding*.yaml


sed -i 's/namespace: .*/namespace: cluster-ops/' install/cluster-operator/*RoleBinding*.yaml


adapt NAMESPACE 

            - name: STRIMZI_NAMESPACE
              value: "*"
#              valueFrom:
#                fieldRef:
#                  fieldPath: metadata.namespace

kubectl create clusterrolebinding strimzi-cluster-operator-namespaced --clusterrole=strimzi-cluster-operator-namespaced --serviceaccount cluster-ops:strimzi-cluster-operator
kubectl create clusterrolebinding strimzi-cluster-operator-entity-operator-delegation --clusterrole=strimzi-entity-operator --serviceaccount cluster-ops:strimzi-cluster-operator
kubectl create clusterrolebinding strimzi-cluster-operator-topic-operator-delegation --clusterrole=strimzi-topic-operator --serviceaccount cluster-ops:strimzi-cluster-operator


kubectl create namespace cluster-ops   
kubectl create -f install/cluster-operator -n cluster-ops

#wait for the cluster opeerator to become available
kubectl get deployments -n cluster-operator
NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
strimzi-cluster-operator   1/1     1            1           2m13s


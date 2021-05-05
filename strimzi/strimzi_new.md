strimzi new

# create the namespace if not existing
kubectl create ns kafka


# download strimzi

latest release from https://github.com/strimzi/strimzi-kafka-operator/releases


# adapt the strimzi files to use the correct namespace

change "myproject" namespace to your namespace name 
in this case we change to "kafka"
(/install/cluster-operator/*RoleBinding*.yaml)

Linux 
$ sed -i 's/namespace: .*/namespace: kafka/' install/cluster-operator/*RoleBinding*.yaml

macos
sed -i '' 's/namespace: .*/namespace: kafka/' install/cluster-operator/*RoleBinding*.yaml

# name space consideration

## single namespace
to deploy the Kafka cluster in the same namespace as the cluster operator nothing else to do

## multi namespace
to deploy Kafka on different namespace the strimi operator needs to be aware of the namespace 
where Kafka clusters will run
look for 060-Deployment-strimzi-cluster-operator.yaml file and locate the STRIMZI_NAMESPACE environment variable

Change valueFrom to value and add your namespaces like below:
# ...
env:
  - name: STRIMZI_NAMESPACE
    value: kafka-cluster-1,kafka-cluster-2,kafka-cluster-3
# ...

install role bindings for each namespace

kubectl apply -f install/cluster-operator/020-RoleBinding-strimzi-cluster-operator.yaml -n <namespace>

kubectl apply -f install/cluster-operator/031-RoleBinding-strimzi-cluster-operator-entity-operator-delegation.yaml -n <namespace>

kubectl apply -f install/cluster-operator/032-RoleBinding-strimzi-cluster-operator-topic-operator-delegation.yaml -n <namespace>


## all namespaces

to let the operator watch all the namespaces 
look for 060-Deployment-strimzi-cluster-operator.yaml file and locate the STRIMZI_NAMESPACE environment variable

Change valueFrom to value and use “*” as the value:
# ...
env:
  - name: STRIMZI_NAMESPACE
    value: "*"
# ...



 create ClusterRoleBindings that grant cluster-wide access for all namespaces to the Cluster Operator

kubectl create clusterrolebinding strimzi-cluster-operator-namespaced --clusterrole=strimzi-cluster-operator-namespaced --serviceaccount kafka:strimzi-cluster-operator

kubectl create clusterrolebinding strimzi-cluster-operator-entity-operator-delegation --clusterrole=strimzi-entity-operator --serviceaccount kafka:strimzi-cluster-operator

kubectl create clusterrolebinding strimzi-cluster-operator-topic-operator-delegation --clusterrole=strimzi-topic-operator --serviceaccount kafka:strimzi-cluster-operator

deploy strimzi
kubectl create -f install/cluster-operator -n kafka


kubectl apply -f kafka-deployment.yml -n kafka



### kafdrop
git clone https://github.com/obsidiandynamics/kafdrop
cd kafdrop

helm upgrade -i kafdrop chart --set image.tag=3.27.0 --set kafka.brokerConnect=kafka-mgb-kafka-bootstrap:9092 -n kafka


## schema registry
cd ~/code/cp-helm-charts/charts/cp-schema-registry
helm install kafka-schema-registry --set kafka.bootstrapServers="PLAINTEXT://kafka-mgb-kafka-bootstrap:9092" . -n kafka

## ksql
cd ~/code/cp-helm-charts/charts/cp-ksql-server
helm install ksql-server --set cp-schema-registry.url="http://kafka-schema-registry-cp-schema-registry:8081",kafka.bootstrapServers="PLAINTEXT://kafka-mgb-kafka-bootstrap:9092",ksql.headless=false . -n kafka

# check ksql
kubectl -n kafka run tmp-ksql-cli --rm -i --tty --image confluentinc/cp-ksql-cli:5.2.1 http://ksql-server-cp-ksql-server:8088

## connect
helm install kafka-connect --set kafka.bootstrapServers="PLAINTEXT://kafka-mgb-kafka-bootstrap:9092",cp-schema-registry.url="kafka-schema-registry-cp-schema-registry:8081" . -n kafka
## lenses
helm install lenses lensesio/lenses --namespace kafka --namespace kafka --set restPort=9991 -f lenses.yaml



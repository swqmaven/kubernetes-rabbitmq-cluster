* RabbitMQ cluster on kubernetes

It's fork and modify from https://github.com/binarin/rabbit-on-k8s-standalone.git

  - New features of this version
    #+BEGIN_SRC sh
      Enabled rabbitmq management plugins
      Add kubernetes services used node port type
      Modify deb source used 163 mirrors
      Used rabbitmq offine-line file replace online download
    #+END_SRC

  - Make some etcd installation available under ~etcd~ name
    #+BEGIN_SRC sh
      kubectl run etcd --image=microbox/etcd --port=4001 -- --name etcd
      kubectl --namespace=demo expose deployment etcd
    #+END_SRC

  - Build a Docker image
    #+BEGIN_SRC sh
      eval $(minikube docker-env)
      docker build . -t rabbitmq-autocluster
    #+END_SRC

  - Initialize k8s secret for erlang cookie
    #+BEGIN_SRC sh
      kubectl create secret generic erlang.cookie --from-file=./erlang.cookie
    #+END_SRC

  - Deploy RabbitMQ cluster
    #+BEGIN_SRC sh
      kubectl create -f rabbitmq-cluster.yaml
    #+END_SRC

  - Config RabbitMQ cluster
    #+BEGIN_SRC sh
      rabbitmqctl add_user admin 1
      rabbitmqctl set_user_tags admin administrator
      rabbitmqctl set_permissions admin ".\*" ".\*" ".\*"
    #+END_SRC

  - Check cluster
    #+BEGIN_EXAMPLE
      kubectl get pods
      kubectl exec rabbitmq-autocluster-2840882472-8sar0 rabbitmqctl cluster_status
      Cluster status of node 'rabbit@172.24.7.9' ...
[{nodes,[{disc,['rabbit@172.24.6.5','rabbit@172.24.7.8',
                'rabbit@172.24.7.9']}]},
 {running_nodes,['rabbit@172.24.6.5','rabbit@172.24.7.8','rabbit@172.24.7.9']},
 {cluster_name,<<"rabbit@rabbitmq-autocluster-2840882472-lkp61">>},
 {partitions,[]},
 {alarms,[{'rabbit@172.24.6.5',[]},
          {'rabbit@172.24.7.8',[]},
          {'rabbit@172.24.7.9',[]}]}]

    #+END_EXAMPLE
    ![image](https://github.com/swqmaven/kubernetes-rabbitmq-cluster/rabbit_mq.png)

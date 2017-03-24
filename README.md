* RabbitMQ cluster on kubernetes

It's fork and modify from https://github.com/binarin/rabbit-on-k8s-standalone.git

  - New features of this version
  ```
     Enabled rabbitmq management plugins
     Add kubernetes services used node port type
     Modify deb source used 163 mirrors
     Used rabbitmq offine-line file replace online download
  ```

  - Make some etcd installation available under ~etcd~ name
    ```
      kubectl run etcd --image=microbox/etcd --port=4001 -- --name etcd
      kubectl --namespace=demo expose deployment etcd
    ```

  - Build a Docker image
    ```
      eval $(minikube docker-env)
      docker build . -t rabbitmq-autocluster
    ```

  - Initialize k8s secret for erlang cookie
    ```
      kubectl create secret generic erlang.cookie --from-file=./erlang.cookie
    ```

  - Deploy RabbitMQ cluster
    ```
      kubectl create -f rabbitmq-cluster.yaml
    ```

  - Config RabbitMQ cluster
    ```
      rabbitmqctl add_user admin 1
      rabbitmqctl set_user_tags admin administrator
      rabbitmqctl set_permissions admin ".\*" ".\*" ".\*"
    ```

  - Check cluster
    ```
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

    ```

    ![image](https://github.com/swqmaven/kubernetes-rabbitmq-cluster/blob/master/rabbit_mq.png)

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

    And we should see some output similar to the following one (where
    the most important bit of information is that ~nodes~ and
    ~running_nodes~ both contain 3 names):
    #+BEGIN_EXAMPLE
      Cluster status of node 'rabbit@172.17.0.3' ...
      [{nodes,[{disc,['rabbit@172.17.0.3','rabbit@172.17.0.4',
                      'rabbit@172.17.0.7']}]},
       {running_nodes,['rabbit@172.17.0.4','rabbit@172.17.0.7','rabbit@172.17.0.3']},
       {cluster_name,<<"rabbit@rabbitmq-deployment-861116474-cmshz">>},
       {partitions,[]},
       {alarms,[{'rabbit@172.17.0.4',[]},
                {'rabbit@172.17.0.7',[]},
                {'rabbit@172.17.0.3',[]}]}]

    #+END_EXAMPLE

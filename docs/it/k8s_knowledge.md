# K8s knowledge
  Learned from youtube posted by nana  [k8s video](https://www.youtube.com/watch?v=X48VuDVv0do)  
# Overview of k8s
  - Open source container orchastration tool
  - Developed by google
  - Helped you to manage containerized applications in different development environment (local,server,cloud or hybrid environment)
  - New trend of future development increased usage of container and it is really difficult to manage containers especially there are a lot of containers to be managed, so k8s comes up to solve this problem.
  - K8s provide 
        High availability or no downtime
        Scalability and high performance
        Disaster recovery - backup and restore
# K8s basic components
  - Node *Worker node or master node*
  - Pod *Small unit of k8s, abstraction over container, usually one application per Pod, each pod get its own internal ip address*
  - Container *Can die easily and each time it will get a new ip address so it is not easy to manage here service comes up to solve this problem*
  - Service *Internal or external service*
  - Ingress *Forward request to service*
  - Configmap *Configuration of service and others*
  - Secret *Store confidential data and it just works like configmap*
  - Volume for data storage *Database data or log data has to be persistent*
  - Replica *Can be configured to create a replica pod*
  - Deployment *All the pod or container will be created by deployment and replica will be configured within a deployment*
  - Stateful set *For application like databases such as mySQL, mongo DB, elastic search etc.*  **It is really tedious to manage a stateful set so databases normally will be hosted outside k8s**      
# K8s architecture  
![k8s_architecture](./images/k8s_architecture.png)
  

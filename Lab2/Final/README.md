# Lab 2: Deploying a request splitting ambassador and a load balancer with Kubernetes

## Objective:
- Learn how to configure and run a request splitter using Nginx.
- Be familiar with the ConfigMap tool in Kubernetes.
- Learn how to use the curl command for requesting an HTTP method.
- Learn how to configure load balancer services
- Get Familiar with load balancing pattern

### Repository: (Contains all the files created within the lab):
https://github.com/GeorgeDaoud3/SOFE4790U-lab2

### Lab Repository: https://github.com/tiwaojo/distributed-sys-labs/tree/master/Lab2/Final

## Discussion [Gateway Routing pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/gateway-routing)
 ### Summarize the problem, the solution, and the requirements for the pattern given in part 1.
 #### Problem and their  Solution :
 - They are different services which require different solutions. A client may need multiple services in order to achieve his target.  Each service has its own API. If any of these services API  has been refactored, code in both the service and client side must be changed. API gateway in the front of those API can be used so that client will contact that API only and API can further call those services based on the clients request. If there is a change in the service , then only the service code and API needs to be updated . Client calls will then be simple.

 - Sometimes , the same service needs to have multiple instances for various purposes such as load balancing . These instances can be in the same or multiple regions . No of instances required depends on the demand. If there is any addition or removal of the instance then the client must be notified of the change. This problem  can be solved done through a gateway, where the gateway contains all the complexity to register or unregister a service instance and there is no need for the client to know the no of service instances

 - Same service may have multiple versions, where these versions are deployed along with the existing versions. Client then needs to be updated each time there is a change in the percentage of traffic being routed to the new  and the old version .Gateway routing pattern can be used here where the new versions can be deployed parallel to existing versions. This way now the gateway contains the complex part and clients don’t need to be updated about the traffic percentage. Any change in version can be refactored in the gateway and clients do not get affected.


 #### Requirements for the pattern:
 - This pattern can be used when multiple services are required by the client.
 - To Simplify client applications.
 - Services that needs to be accessed in multiple regions
 - When you don’t want the client to be affected if there is any change in the code or if there is a change in the number of instances  .
 - Multiple versions of the service can be accessed by the client.
 - When a user/client requires route requests from the laptop to the VM etc.

 ### Which of these requirements can be achieved by the procedures shown in parts 2 and 3?
 - Multiple instance of  services, simply client applications, multiple versions of the services and route request from laptop to VM, these requirements can be achieved by procedures in part 2 and 3
## Design:
 ### Why is autoscaling usually used? How is autoscaling implemented? How is auto scaling different from load balancing and request splitter?
 - Autoscaling is usually used to increase or decrease the memory , compute or the network resources that have been allocated according to the demand . This pattern is very useful and cost effective as well . For example , when there was an increase in the demand for Netflix , the resources were increased . Neftlix uses a distributed system , and with the help of autoscaling, Netflix was able to increase their network resources to meet that demand . Also when the demand decreases , the network resources are also reduced hence managing the cost . Autoscaling can be implemented by using horizontal scaling or vertical scaling approaches . In Horizontal scaling , instances of the resources are added or reduced based on the demand or requirement . In vertical scaling, the capacity of the resource can be increased or decreased depending on the demand .
 - In Load Balancing and request splitter , the container required and resources like the pods are fixed . Load balancer sends the traffic to the resources by looking at their availability whereas in request slitter , we already decide how the traffic should be splitted based on the percentage . In Autoscaling , the instances of the resources or the capacity of the power is scalled based on the demand or incoming traffic to that site.
### How autoscaling is implemented in GKE?
- Create a deployment YAML file with resource limits. E.g. cpu limits
   ``` yaml
        resources:
          limits:
            cpu: 500m
          requests:
            cpu: 200m
   ```
    -  Use the following command to create your HPA (Horizontal Pod Autoscaller)
    ```sh
    kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10
    ```
    `--min`: The lower limit for the number of pods that can be set by the autoscaler. If it's not specified or negative, the server will apply a default value.
    `--max`: The upper limit for the number of pods that can be set by the autoscaler. Required.
    [Kubernetes.io/autoscale](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale)

    ```sh
    # check the current status of the newly-made HorizontalPodAutoscaler
    kubectl get hpa
    ```
    ```sh
    # Run this in a separate terminal
    # so that the load generation continues and you can carry on with the rest of the steps
    kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"
    ```
    Alternatively, you could simply send multiple requests through the browser by refereshing several times.

    In a new tab use the below command to watch for hpa pods
    ```sh
    # type Ctrl+C to end the watch when you're ready
    kubectl get hpa php-apache --watch
    ```
    ### Resource used: [HorizontalPodAutoscaler Walkthrough](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/#run-and-expose-php-apache-server)
- How autoscaling is different than load balancing and request splitter?
    - Request splitting requires the Pods have already been deployed and you are simply distributing the workload to specific Pods at runtime. I.e. you can specify a particular pod in a replica set absorb a certain percentage of the networks traffic. This method will likely leave some Pods idle while the others are overworked
    - Load balancing is similar to request splitting where as rather than having a particular Pod carry most of the traffic, the traffic is distributed dynamically to the replica set. This ensures no single Pod is ingesting the applications bandwidth of data.
    - Auto Scaling is similar to load balancing as where load balancing has to distribute its workload during among its predefined replica set, auto scaling simply adds another Pod to its replica set to ingest some of the bandwidth.

## Report (GDocs Link)
 ~~- [Lab2 Report](https://)~~

## Recordings (GDrive Link)
- [Request Splitting & Loadbalancing](https://drive.google.com/file/d/1tM7bU5EOSj7sT95l9JrpXweKl9De-XAw/view?usp=sharing)
- [Autoscaling](https://drive.google.com/file/d/1U0UBBNsve8n61z04RpxyA8I9EEwAXjDd/view?usp=sharing)
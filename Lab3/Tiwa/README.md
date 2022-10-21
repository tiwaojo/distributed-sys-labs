# Lab 3: Deploying a Circuit Breaking ambassador and a Function-as-a-Service (FaaS)

## Objective:
1. Learn how to create a service using NodeJS.
2. Learn how to create a Docker image.
3. Learn how to configure and use a circuit breaker using Nginx.
4. Get Familiar with FaaS.
5. Learn how to configure and use OpenFaas on GCP
6. Get Familiar with Decorator Pattern

## Repository: (Contains all the files created within the lab):
https://github.com/GeorgeDaoud3/SOFE4790U-lab3

### Discussion [Gateway Routing pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/gateway-routing)
- Which of these requirements can be achieved by the procedures shown in parts 2 and 3?
    - The request splitter can be a good procedure to meet the requirements of the pattern as it allows the clients to consume services running in multiple regions due to latency as well as access multiple versions of the service. Developers are also able to slowly deploy the newer version of the service into production and roll back on any issues without the clients knowledge
    - The autoscaler is also a good procedure to follow for similar reasons as above but also has the added benefit of scaling out during peak demands.
### Design

```sh
    curl -SLsf https://cli.openfaas.com | sudo sh && \
    export OPENFAAS_URL="34.95.40.92:8080" && \
    PASSWORD=$(kubectl get secret -n openfaas basic-auth -o jsonpath="{.data.basic-auth-password}" | base64 --decode; echo) && \
    echo $PASSWORD
    echo -n $PASSWORD | faas-cli login --username admin --password-stdin
```

- Why autoscaling is usually used?
    - To utilize as much resource as possible while having the flexibility to meet demand. The developer also does not have to run the risk of having idle Pods during runtime.
- How autoscaling is implemented?
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
    kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://<external-ip>; done"
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
- [Lab2 Report](https://)

## Recordings (GDrive Link)
- [Request Splitting & Loadbalancing](https://drive.google.com/file/d/1tM7bU5EOSj7sT95l9JrpXweKl9De-XAw/view?usp=sharing)
- [Autoscaling](https://drive.google.com/file/d/1_NbYcT02IcDJO2UN0qXSfid5GPh7OPfC/view?usp=sharing)
    - This video goes over the requested limit. Feel free to skip to the later parts of the video as my browser crashes mid recording.
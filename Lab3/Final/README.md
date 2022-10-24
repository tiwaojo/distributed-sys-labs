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

### Discussion [Health Endpoint Monitoring pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/health-endpoint-monitoring)
- Focus on the problem being solved by the pattern, how is it solved? and the
requirements needed for the solution
    - Solution: Create a health monitoring endpoint on you application. It will check for
    cloud storage/database for availability and response time
    - Requirements:
        - System should be able to validate the response code
        - Detect errors from the response
        - Measure response time to determine latency and execution duration
        - Check for expired server certificates
- Summarize the problem, the solution, and the requirements for the pattern given in part1
    - To ensure the business requirements of your applications are working to business specifications, monitoring metrics such as availability and performance is vital. The service can fail partially or completely. Quick reactions to such events can impact the profitability of a business. A solution to this is to have cron jobs at which a health monitor request is sent to the service to determine a small log of its state.
    - Which of these requirements can be achieved by the procedures shown in parts 2 and 3?
        - Measure response time: This can be achieved by enabling the body of your response to include the execution duration of your request
        - Detect Errors: Although the response one might get will be a `200 OK` message, the body of the message might have a log of whatever error occured
### Design

- Kubernetes provides  persistent volumes. Why such a feature can be  important?
    - Due to the fragmented nature of kubernetes, containers do not maintain their storage. Each container discards its storage once it is no longer active.
    - How to implement it?
        - The container/pod will need to have a mounted volume. This is possible to implement in the YAML config file.

- Provide an example in which persistent volumes are needed. Configure a YAML file to implement the example. Run it and test the creation of persistent volume and its ability to provide the required functionality within the example.
    - A web application with a database might require its state to be persistent between executions
    - A YAML with this configuration might look like below:
    - Proof:
        - ![Persistent Volume MongoDB](init_pvc_mongodb.png)

```sh
# used the command below to simulate a scale down/up to test the PersistedVolume. Volume could not be persisted
    kubectl scale -f pv.yaml --replicas=0; kubectl scale -f pv.yaml --replicas=1
```

``` yaml
        apiVersion: v1
        kind: Service
        metadata:
        name: mongodb-service
        spec:
        type: LoadBalancer
        ports:
            - port: 3306
        selector:
            app: mongodb
        ---
        apiVersion: apps/v1
        kind: Deployment
        metadata:
        name: mongodb-deployment
        spec:
        replicas: 1
        selector:
            matchLabels:
            app: mongodb
        template:
            metadata:
            labels:
                app: mongodb
            spec:
            containers:
                - image: bitnami/mongodb:latest
                name: mongodb
                env:
                    - name: MONGODB_SHELL_CREATE_DATABASE_USERNAME
                    value: user
                    - name: MONGODB_ROOT_USER
                    value: user
                    - name: MONGODB_ROOT_PASSWORD
                    value: sofe4790u
                    - name: MONGODB_SHELL_CREATE_DATABASE_NAME
                    value: myDB
                ports:
                    - containerPort: 3306
                    name: mongodb
                volumeMounts:
                    - mountPath: /var/lib/mongodb/db-data # mount path for our application within our application
                    name: mongodb-pv
            volumes:
                - name: mongodb-pv
                persistentVolumeClaim:
                    claimName: mongodb-pv-claim
        ---
        apiVersion: v1
        kind: PersistentVolumeClaim
        metadata:
        name: mongodb-pv-claim
        namespace: default
        # annotations:
        #   anthos-migrate.gcr.io/vm-id: vm-1
        #   anthos-migrate.gcr.io/vm-data-access-mode: Streaming
        #   anthos-migrate.gcr.io/run-mode: TestClone
        spec:
        accessModes:
        - ReadWriteOnce
        # storageClassName: vls-storage-class
        resources:
            requests:
            storage: 5Gi

 ```
## Recordings (GDrive Link)
- [Fakeerrormode Deployment](https://drive.google.com/file/d/1MG2yGQBSiC6tg2vh0WTFmwORDIOzDkEP/view?usp=sharing)
- [OpenFAAS](https://drive.google.com/file/d/1M1ac7Vurc8818jZS840tTTD1mD9YcFRY/view?usp=sharing)
    - This video goes over the requested limit. Feel free to skip to the later parts of the video as my browser crashes mid recording.

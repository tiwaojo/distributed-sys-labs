# Lab 2: Deploying a request splitting ambassador and a load balancer with Kubernetes #

## Report (GDocs Link)
- [Lab2 Report](https://docs.google.com/document/d/1GJHmIncwdRjKDRSpfJcif2BP5ITUePLPujTdtfGqsQQ/edit?usp=sharing)

## Discussion: 
 ### Problem and their  Solution : 
 -They are different services which require different solutions. A client may need multiple services in order to achieve his target.  Each service has its own API. If any of these services API  has been refactored, code in both the service and client side must be changed. API gateway in the front of those API can be used so that client will contact that API only and API can further call those services based on the clients request. If there is a change in the service , then only the service code and API needs to be updated . Client calls will then be simple. 

 -Sometimes , the same service needs to have multiple instances for various purposes such as load balancing . These instances can be in the same or multiple regions . No of instances required depends on the demand. If there is any addition or removal of the instance then the client must be notified of the change. This problem  can be solved done through a gateway, where the gateway contains all the complexity to register or unregister a service instance and there is no need for the client to know the no of service instances 

 -Same service may have multiple versions, where these versions are deployed along with the existing versions. Client then needs to be updated each time there is a change in the percentage of traffic being routed to the new  and the old version .Gateway routing pattern can be used here where the new versions can be deployed parallel to existing versions. This way now the gateway contains the complex part and clients don’t need to be updated about the traffic percentage. Any change in version can be refactored in the gateway and clients do not get affected. 














## Recordings (GDrive Link)
- [Lab Steps](https://drive.google.com/file/d/1enYkfeTG57Is2PeCdWJtIkwo7wMYN7w8/view?usp=sharing)
- [Autoscaling](https://drive.google.com/file/d/1U0UBBNsve8n61z04RpxyA8I9EEwAXjDd/view?usp=sharing)

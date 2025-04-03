
## Why Deployment Strategy is required?
We don't deploy the application or container just once, usually when a new requirement comes up when new code develop, bug-fixes, new feature comes then new release comess that is new version. How ever the customer 
may still connected to older version, Then we use Deployment Strategy to deploy new version.

## Rollout:
Switch to version v1 to version v2 or deploying new version is known as rollout. Also known as new release.

version v1 -->> version v2 -->> version v3 -->> version v4

## Rollback: [undo]
Switch to older version like version v3 to version v2 this is rollback. This also known as undo in K8s.

version v4 -->> version v3        version v2 -->> version v1

- Note:

  The  challenge is how to rollout without **downtime**. We need a strategy to ensure thye application is always available.

**Deployment Strategies:**

- [x] Rolling Update Deployment / Ramped Deployment
- [x] Recreate Deployment
- [ ] Blue-Green Deployment
- [ ] Canary Deployment
- [ ] A/B testing Deployment
- [ ] Shadow Deployment / Dark Launching
- [ ] Big Bang Deployment
- [ ] CD 


*But, Kubernetes in-build supports only two that is RollingUpdate Deployment & Recreate Deployment Strategy.*

## Recreate Deployment strategy:
- Consider client connect to verion-v1, then in Recreate 1st delete version-v1 all Pods, then launch new version-v2 Pods, Such type of Deployment strategy is known as Recreate Strategy.
- Deletes all old pods before starting new ones.
- In this deployment strategy have Downtime.

## RollingUpdate Deployment Strategy:
- This Default Deployment Strategy of K8s.
- Gradual replacement of old pods.
- In this Deployment method, we run multiple replicas of version-v1, when version-v2 is released, RollingUpdate don't delete all version-v1 replicas/copies at once,
- 1st create new Pod(Replica) of new version-v2
- 2nd once new version-v2 Pod one copy run successfully then only delete older version-v1 Pod(Replica), And Client side traffic diverted to new version-v2 Pod without Downtime.
- 3rd This process repeat again again untill all new version-v2 Pods replaces with older version-v1 Pods.
- This Rolling Update strategy ensures that application stay available & client do not experience downtime.



## Deployment:
- Deployment is one of the best resource type of K8s .
- Deployment resource type gives RollingUpdate & Recreate Strategy.
- Deployment also gives support ReplicaSet in-bild resorce type, So by using Deployment resource type we can easily maintain desired Pods numbers.
- As we know, ReplicaSet does not know which is Older version-v1 which is newer version-v2, So while RollOut using RollingUpdate Strategy Deployment one who maintain or understand which is older version and which
  is newer version Pod, So Deployment Resource Type is used.
- by default Strategy is RollingUpdate.

 **Deployment**  ---create--->>  **ReplicaSet**  ---create/maintain--->>  **Pods**  ---launch--->> **Container**  ---run--->>  **Application**
  

# Rolling Update Deployment Strategy working:
For this Project i used minikube K8s platform.

## Steps:
1. Create Deployment YAML file.
2. Create Service YAMl Code using NodePort type.
3. Roll out- Now we add new version of image in Deployment.
4. Roll Back (Undo) -
5. Automation file


## Step 1: Deployment & Service resource type YAML file:
- Here i use Deployment resource type for create pods and monitoring desired state of pods use ReplicaSet in Deploymnet, & Create NodePort type service load balancer for outside world connectivity.

- Here i use version v1 of image, Deployment keep on monitoring that image version.

  ![Code-deploy-svc](https://github.com/user-attachments/assets/9a71a571-dac7-47d8-8f1e-2898dcccf897)

- Run File with v1 /tag v1 of image:

       kubectl apply -f rolling-update.yml

- Get Deploy cmd:

       kubectl get deploy

  ![Deploy-created](https://github.com/user-attachments/assets/9589c796-adcc-4613-b51e-7705b9f9e822)

- Describe Deploy we see version v1 of image is used:

       kubectl describe deploy ps-deploy

   ![image](https://github.com/user-attachments/assets/37700687-6273-4f92-9360-150e0d618812)


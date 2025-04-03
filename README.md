
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
  
*** 

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

  ![Describe-deploy](https://github.com/user-attachments/assets/fa1f910e-809b-40cc-b153-7f3bc21c8943)

- Here we can also see that Deployment creates ReplicSet and ReplicaSet creates Pods; And we also created Service:
  
  ![Svc](https://github.com/user-attachments/assets/bc6696c8-7412-43c9-84ea-a74b1b98d47f)

- On Browser we see our server v1: (use node IP : NodePort No -->>  http://192.168.59.115:30055/)
  
  ![browesr-1-look](https://github.com/user-attachments/assets/538ea82f-1de7-420d-b4a0-177f330daa5d)


## Step 2: Rollout with new version v2:
**Manual/Offline Way:
- Now i use new version image that version v2 in my Deployment file: (Only add new image/tag, We don't change other code)
  
   ![version-2-deployment](https://github.com/user-attachments/assets/3bfa38a3-eaed-442a-9340-35a663ec95d5)

- We Just apply that file again:

      kubectl apply -f rolling-update.yml
  
  ![new-deploy-congi](https://github.com/user-attachments/assets/06a3962f-e861-4365-9c56-75ab777b42f5)

- Now, Pods changing with new versions, 1st one new version v2 pod created then old version v1 pod deleted & then this process keep on running till all pods replaces with new version v2.
- Here, Deployment Helps to ReplicSet to identify new version v2 pods and old version v1 pods for maintain desired numbers Pods using new/current version v2 of Pods

      kubectl get pods -w

  ![Pod-watch](https://github.com/user-attachments/assets/45f0f2e4-ad9c-47e8-b2a9-c8817008c8a8)

- Here we see new version pods are running:
  
      kubectl get pods

  ![pods-name](https://github.com/user-attachments/assets/e5d37849-7e96-4468-8587-a7e31e72263a)

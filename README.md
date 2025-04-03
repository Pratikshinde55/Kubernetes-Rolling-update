
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


## Step 2: Rollout with new Version:
We can do Roll Out by using two approach:
1. **Offline way (That is add new version in YAML file and apply that file again)**
    - Now i use new version image that `Version v2` in my Deployment file: (Only add new image/tag, We don't change other code)
  
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

    - Here we can see in the new version image show in Deployment Description:

           kubectl describe deploy ps-deploy

      ![describe-v2](https://github.com/user-attachments/assets/023dc455-23c8-44f0-a29f-a9458b285080)

    - We see new version on Browser: (No Downtime new Version v2 comes, Client don't face any downtime, Same link same port we only rollout version v2)

      ![new-v2-browser](https://github.com/user-attachments/assets/faafa3dc-723c-40d0-a065-7c493aedba52)

    - Kubectl rollout status cmd:

           kubectl rollout status deploy ps-deploy
  
      ![rollout-image-1](https://github.com/user-attachments/assets/e1634b62-b203-4c58-9688-fda50015499b)

    - kubectl rollout history cmd:

           kubectl rollout history deploy ps-deploy

      ![rollout-2](https://github.com/user-attachments/assets/cf34e619-f8b4-4972-b69f-567effb177df)

2. **Rollout to Version v3 using online way:**
    - Here i use Online way to add new version/tag of image/app in my deployment file.

    - Here i use kubectl set command to set new version of image with respect to my Deployment Pod by using Container Name:

           kubectl set image  deployment ps-deploy my-c1=pratikshinde55/apache-webserver:v3

      ![New-v3-set](https://github.com/user-attachments/assets/2e2c9ea1-2dd6-410f-9079-d3a59c738b76)

    - We See in kubectl rollout status:

           kubectl rollout status deployment ps-deploy

      ![new-version-3-rollout](https://github.com/user-attachments/assets/0bb77571-c303-4499-9edc-8f325e63b39f)

    - we see also rollout history:

           kubectl rollout history deployment ps-deploy

      ![v3-history](https://github.com/user-attachments/assets/d112c599-0cda-4815-b3c7-7dee5ba1bb92)

    - Also see in Deployment description the new image version added by using set commmand:

           kubectl describe deployment ps-deploy
  
    - New Version v3 see Client on Browser:

      ![v3-browser-look](https://github.com/user-attachments/assets/95bc4a00-6568-41f4-a277-6908da5659a9)


## Step 3: RollBack or Undo:
- We can also here do RollBack by using two methods:
 1. Online way (Use kubectl rollout undo command)
 2. Offline way (Go inside YAMl file and update older version image tag)

- RoLL Back
1. **Online Way:**
     - `kubectl rollout undo` command:(This command only do one roll back version that is v3 to v2 for current project)
  
             kubectl rollout undo deployment ps-deploy

        ![kubectl-rollout-undo](https://github.com/user-attachments/assets/e20d50d9-ed6f-4619-ba4e-e483575256e3)
            
     - see on Client side Browser Client see older version v2 without downtime same things happens like RollOut:

      ![browser-rollback](https://github.com/user-attachments/assets/a8a9f3eb-5bf2-4643-949d-2084c3ea7ae2)

     - Now i Direct Roll Back to version v1 by using  `--to-revision=` this option:
  
            kubectl rollout undo deployment ps-deploy  --to-revision=1
       
        ![rollback-undo-v1](https://github.com/user-attachments/assets/85276255-3b03-47d4-bb8b-e3ed9e09bef2)

      - See on Browser:

        ![v1-broswer-byrollback-undo](https://github.com/user-attachments/assets/3446a5bb-f37d-4d98-94c6-924b570c50e3)

2. **We can do same roll back using offline file:**

     - Go inside YAML file and add old version of image & then apply that YAML file
     - our RollBack completed 

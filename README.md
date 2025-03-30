
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

- Rolling Update Deployment / Ramped Deployment
- Recreate Deployment
- Blue-Green Deployment
- Canary Deployment
- A/B testing Deployment
- Shadow Deployment / Dark Launching
- Big Bang Deployment
- CD 

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





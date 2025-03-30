
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

Deployment Strategies:

- Rolling Update Deployment / Ramped Deployment
- Recreate Deployment
- Blue-Green Deployment
- Canary Deployment
- A/B testing Deployment
- Shadow Deployment / Dark Launching
- Big Bang Deployment
- CD 


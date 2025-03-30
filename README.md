
## Why Deployment Strategy is required?
We don't deploy the application or container just once, usually when a new requirement comes up when new code develop, bug-fixes, new feature comes then new release comess that is new version. How ever the customer 
may still connected to older version, Then we use Deployment Strategy to deploy new version.

## Rollout:
Switch to version v1 to version v2 or deploying new version is known as rollout. Also known as new release.

version v1 -->> version v2 -->> version v3 -->> version v4

## Rollback: [undo]
Switch to older version like version v3 to version v2 this is rollback. This also known as undo in K8s.

version v4 -->> version v3        version v2 -->> version v1




# rollout & versioning
- k rollout status deployment/deploy_name --> shows rollout status
- k rollout history deployment/deploy_name
- k rollout history deployment/deploy_name --revision=1
## Deployment strategy
- Recreate
- Rolling Update(default)

- Deploment creates Replicaset
- To undo change
- k rollout undo deployment/deploy_name
- k rollout undo deployment/deploy_name --to-revision=1


- Change-cause -> save the changing commnad 
  - use --record flag --> kubectl set image deployment/deploy_name container_name=nginx:1.17 --record
  - now CHANGE-CAUSE is viewed when running --> k rollout history deployment/deploy_name
  - Change cause is added as annotation -> kubernetes.io/change-cause
- Rollout --> is the process of creating containers in BE
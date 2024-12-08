# rollout (means deploying new version)
- rollout creates a new deployment
# rollout & versioning
- k rollout status deployment/deploy_name --> shows rollout status
- k rollout history deployment/deploy_name --> history of deployument revsions and versions
- k rollout history deployment/deploy_name --revision=1
## Deployment strategy
- Recreate
- Rolling Update(default)
- k apply -f deployment_file.yml -> file should have the new changes
- k set image deployment/deploy_name container_name=image_name:image_version
- for Recreate:
  - old replicaset replicas is set to 0
  - new replicaset replicas scaled up to the required num
- for RollingUpdate:
  - old replicaset replicas is scaled down one by one (5 - 3 - 2 -0) 
  - new replicaset replicas is scaled up one by one (0 - 3 - 5)

- Deployment creates Replicaset
- To undo change
- k rollout undo deployment/deploy_name
- k rollout undo deployment/deploy_name --to-revision=1

- if I have app with rollouts revisions 1,2,3 and I made a rollout to revision 2 --> I will see that k8s will keep revision 1,3,4 (as 2 is same as 4 and using the same defintion file)
- Change-cause -> save the changing commnad 
  - use --record flag --> kubectl set image deployment/deploy_name container_name=nginx:1.17 --record
  - now CHANGE-CAUSE is viewed when running --> k rollout history deployment/deploy_name
  - Change cause is added as annotation -> kubernetes.io/change-cause
- Rollout --> is the process of creating containers in BE
- maxUnavailable --> how many pods can be down in updating deployment
- maxSurge --> how many pods can we go above num of pods specified in replica
  - For example: in a 4 pod deployment, when we update we can only shut down 1(maxUnavaible) pod at a time and also can only have 4+1(num of pods+ maxSurge) pods at specific time 
  - both could be percentage of absolutes num
  - ensures these balance between (need to update the app) and (keep the app availalbe)
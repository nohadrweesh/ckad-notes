- in linux to get the running user `whoami`
- to get live changes of a command --watch `k get pods --watch`
- to get contianer logs either cat the specific log file if you know it or use logs command:
  - k logs pod_name
  - k exec pod_name -- cat log_path
- Get objects without headers
  - k get pods --no-headers | wc -l
- List all objects
  - k get all
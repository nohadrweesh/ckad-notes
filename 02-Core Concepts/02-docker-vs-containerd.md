- History: Docker vs ContainerD:
  - K8s built to manage Dokcer first
  - Container Runtime Interface CRI --> to handle other container runtime
  - CRI --> Open Container Initiative (OCI) :
    - imagespec
    - runtimespec
  - But docker don't commit to CRI as it was invented before it --> DockerShim
  - Docker is a set of tools (Volumes, CLI, Auth, ContainerD)
  - ContainerD comply with CRI
  - In K8s v1.24 support for dockershim is removed
  - ContainerD:
    - Comes with ctr --> not very user friendly and only support limited features --> used for debugging only
      - ctr images pull
    - nerdctl --> docker-like CLI ,supports docker-compose
      - nerdctl run --name name image:tag -p port:port
    - crictl --> cli for CRI compiatble container runtimes like rkt and containerd
      - not ideally to create containers ,mainly for some deubgging
      - works with kubelet so eventaully kubelet would delete containers created using crictl out of its knowledge (danger)
      - beside some listing images and containers commands ,can also ls pods 
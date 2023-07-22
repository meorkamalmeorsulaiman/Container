# Containerization
This is the place for my containerization learning


# Docker Technology

1. Runtime - Containerd & runc
   - runc:
      - Low-level runtime
      - Interface with the underlying OS
      - Start & stop container
      - CLI wrapper
      - Replaced LXC as the interface layer with the host OS
      - Sole purpose in life - create containers
   - containerd:
      - Manages container lifecycle
      - sole purpose in life is to manage container lifecycle operations such as start, stop, pause and rm
      - It is available as a daemon
      
3. Daemon - dockerd
   - exposing API
   - managing images, volumes, networks
   - provide easy-to-use interface
4. Orchestrator - swarm/kubernetes

# What happen when you create a container

1. Run docker run 
2. Docker client converts into API payload and POST to API endpoint exposed by docker daemon
3. API endpoint can be exposed locally or over the network 
4. The daemon received the create command and call the containerd
5. containerd uses runc to create the container 
6. The contianer process started as runc child process and exited as soon as the container started



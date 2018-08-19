# Workshop: Intro to Container Managment using Kubernetes

**TODO:**

- change image used in deployment & service to be the same. Use a multi-container app if it's feasible for the timeframe.
- add step of creating deployment and service with manifest: put manifest into /examples folder and add apply/etc commands
    - put manifest files into this repo (/examples)
- add step to deployment exercise to kill one and have it spin back up
- Add exercise for updating app

- Topics to be ready to address:

    * Docker integration
        - Docker networking expansion
            - [Container Network Model (CNM) design spec]()

    * Linux integratioons
    * Windows kernal integations
    * Maintain cluster in EC2, EKS, GCP
    * Set up cluster from scratch

## Agenda 

1.5 hours
Lightning talk on container orchestration
Workshop with/Kube concepts throughout

### Container Orchestration & Kubernets Lightning Talk
    - Container management outline:
        - Summary
            - Interaction with containers is inevidible
            - Abstract underlying infrastructure via an orchestrator like Kube
            - Efficiencies in container management - standardization
            - New way to deploy and manage
            - Differences in tools
        - Why Container Management?
        - Container-based Infrastructure
        - Docker Overivew
        - Deploying with containers
        - Clusters & Cloud Architecture
        - What is Container Orchestion?
        - Container management systems
        - Container management concepts
            - Within reason detail about components like pods, volumes, networking, 
        - Tools Overview
        - Into Kubernetes:
            - The Kubernetes way
                - pods, in prod, volumes, networking

## Workshop

- Exercises
    - *SETUP:* Set up a Kubernetes cluster, the quick way for playing
    - Build pod with multiple containers
        - Add service
        - Add deployment
    - Delpoy to cluster
    - Manage
    - Add volume

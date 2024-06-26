# Overview
- Container = software package that contains an app and all its dependencies
	- Lightweight because it only contains the app and its dependencies
- Run on a Container Engine (such as Docker)
	- A Container Engine runs on a host OS
- A **Container Orchestrator** is a software platform for automating the deployment, management, scaling, etc. of containers
	- Ex. Kubernetes & Docker Swarm
- **Microservice Architecture** = an approach to software architecture that divides a larger solution into smaller parts called microservices that are then run in containers that can be orchestrated
# Virtual Machines vs Containers
|                      | Virtual Machines                                                  | Containers                                                                                         |
| -------------------- | ----------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| Speed                | Can take several minutes to boot                                  | Boot up in milliseconds                                                                            |
| Size                 | Gigabytes                                                         | Megabytes                                                                                          |
| Resource Requirement | Far higher CPU/RAM requirements                                   | Very low resource demand                                                                           |
| Portability          | Can be moved between physical systems running the same hypervisor | More portable due to being smaller and faster                                                      |
| Isolation            | More isolated due to running their own OS                         | Less isolated because they all rely on the same OS (if the OS crashes, all the containers go down) |

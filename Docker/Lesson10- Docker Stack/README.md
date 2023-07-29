# Docker Stack

## Use file to deploy or manage swarm lifesycle

- Is is similar to Dockerfile named `compose.yaml`
- However, it doesn't have build feature
- Sample compose file in `compose.yaml` which provision prometheus and grafana
- To deploy use:

```bash
docker stack deploy -c [filename]
```

- Once deployed, you can validate it was replicated:

```bash
docker stack ps [service name]
```

- Once validate access as usually.
- More command that can be use:

```sh
$docker stack ls # list service running
$docker stack rm [service name] #delete running service
$docker stack ps [service name] #validate running replicas
```
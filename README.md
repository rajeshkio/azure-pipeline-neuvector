# How to run docker on VMs to run azure agent inside docker


https://learn.microsoft.com/en-us/azure/devops/pipelines/agents/docker?view=azure-devops#mounting-volumes-using-docker-within-a-docker-container

 If a Docker container runs inside another Docker container, they both use host's daemon, so all mount paths reference the host, not the container.
 

```shell
docker run --network="host" --cap-add SYS_PTRACE \
	-v /var/run/docker.sock:/var/run/docker.sock \
	 -v /usr/bin/docker:/usr/bin/docker \
	 -v /usr/local/bin/docker-compose:/usr/bin/docker-compose \
	 -v /azp/_work:/azp/_work --add-host host.docker.internal:host-gateway \
	 --name azureagentdocker -e AZP_URL=$AZP_URL \
	 -e AZP_TOKEN=$AZP_TOKEN  \
	rk90229/docker-azure-agent:v1
```

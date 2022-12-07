# How to run docker on VMs to run azure agent inside docker


https://learn.microsoft.com/en-us/azure/devops/pipelines/agents/docker?view=azure-devops#mounting-volumes-using-docker-within-a-docker-container

 If a Docker container runs inside another Docker container, they both use host's daemon, so all mount paths reference the host, not the container.
 
Create Personal Access Toekn from Azure devops dashboard. 

1 - Login to azure dashboard
2 - Click on user settings --> personal access token
3 - New Token 
4 - Give it a name e.g Azure-pipeline, choose custom defined under Scopes
5 - Click on Show all scopes, on Agent pools check mark Read & Manage, click on create.
6 - Copy the token and keep it safe, we will use this later.


Run command on VM

1 - Make sure docker is installed on the VM
2 - Run the below commands:-

```shell
export AZP_URL=https://dev.azure.com/rajeshkumar0292
export AZP_TOKEN="Enter your token"

docker run --network="host" --cap-add SYS_PTRACE \
	-v /var/run/docker.sock:/var/run/docker.sock \
	 -v /usr/bin/docker:/usr/bin/docker \
	 -v /usr/local/bin/docker-compose:/usr/bin/docker-compose \
	 -v /azp/_work:/azp/_work --add-host host.docker.internal:host-gateway \
	 --name azureagentdocker -e AZP_URL=$AZP_URL \
	 -e AZP_TOKEN=$AZP_TOKEN  \
	rk90229/docker-azure-agent:v1
```


Note - I have prebuilt the image, if you need you can use the dockerfile to modify anything. Please follow the above microsoft link.


Create pipeline

1 - create a file to run a pipeline. eg azure-pipeline.yaml and push to your repo on github.
2 - Login to azure devops dashboard https://dev.azure.com/.
3 - Create a project
4 - To create a new pipleine, click on New pipeline
5 - Choose github
6 - It might ask for authorisation, login and authorise
7 - Choose the appropriate repo created in the first step
8 - On the configure tab, select Existing Azure pipelines YAML files, select the branch and path as per your repo and file, and click continue
9 - Save or run the pipeline, it might ask to permit, allow it.


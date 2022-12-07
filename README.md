## How to run docker on VMs to run azure agent inside docker


Reference : https://learn.microsoft.com/en-us/azure/devops/pipelines/agents/docker?view=azure-devops#mounting-volumes-using-docker-within-a-docker-container

 Note : If a Docker container runs inside another Docker container, they both use host's daemon, so all mount paths reference the host, not the container.
 
Steps

### 1 - Create Personal Access Token from Azure devops dashboard. 

a - Login to azure dashboard

b - Click on user settings --> personal access token

c - New Token 

d - Give it a name e.g Azure-pipeline, choose custom defined under Scopes

e - Click on Show all scopes, on Agent pools check mark Read & Manage, click on create.

f - Copy the token and keep it safe, we will use this later.


### 2 - Run command on VM

a - Make sure docker is installed on the VM

b - Run the below commands:-

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

### 3 - Add connection string

a - Login to Azure devops dashboard.
b - Select project
c - Select project settings at the left bottom.
d - Go to service connection.
e - New service connection.
f - Select docker registry.
g - Change Docker Registry URL to https://registry.hub.docker.com
h - Enter Docker ID, Docker Password and gitve connection a name.
i - You can select to Grant access permission to all pipelines depending on your use case and save.


### 4 - Create pipeline

a - create a file to run a pipeline. eg azure-pipeline.yaml and push to your repo on github.

b - Make sure to use the connection name created above for nvContainerRegistry and containerRegistry.

c - Login to azure devops dashboard https://dev.azure.com/.

d - Create a project

e - To create a new pipleine, click on New pipeline

f - Choose github

g - It might ask for authorisation, login and authorise

h - Choose the appropriate repo created in the first step

i - On the configure tab, select Existing Azure pipelines YAML files, select the branch and path as per your repo and file, and click continue

j - Save or run the pipeline, it might ask to permit, allow it.

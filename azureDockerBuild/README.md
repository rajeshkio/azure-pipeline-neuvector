## Steps:

 - Clone the Repo https://github.com/rajeshkio/azure-pipeline-neuvector.git
 - cd azure-pipeline-neuvector
 - install vagrant. Follow https://developer.hashicorp.com/vagrant/tutorials/getting-started/getting-started-install 

   For ubuntu
   ```shell
         sudo apt-get install vagrant -y
   ```

 - run **vagrant up**. This will start the VM and install docker in it.
 - Build the Docker azure agent 

   ```shell
      docker build -t "$yourdockerhubusername"/azure-agent-docker:v1 .
      docker login // optional but recommended
      docker push "$yourdockerhubusername"/azure-agent-docker:v1  // optional but recommended
   ```
 - Run the docker container:
    
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
		"$yourdockerhubusername"/azure-agent-docker:v1
      
    ```

 

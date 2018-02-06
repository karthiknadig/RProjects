# Shiny Docker Container
# How to run this project?
1. Make sure you have docker installed.
1. In the command line go to `Shiny` directory and build the docker image:
`G:\RProjects\Docker\Shiny> docker build -t myShinyApp:test .`
1. Run the following command to start the shiny app in the container: `G:\RProjects\Docker\Shiny> docker run -it -p 5000:5000 myShinyApp:test Rscript --vanilla /work/script.R`
1. Open a browser, and go to http://localhost:5000 

# How does this sample work?
First the R script should refer to files from either a relative path, a known destination, or web location. Here I use a known destination:
```
airports <- read.csv("/work/airports.dat", header = FALSE, stringsAsFactors = FALSE)
```
 
Next we need to tell Shiny to start at a known port. Either use runApp or setOptions to do this. I used runApp in the sample, to set the internal port to:
```
runApp(shinyApp(ui=ui, server=server), port=5000, host="0.0.0.0")
```
 
I am only showing the relevant parts here from the docker file. The install the needed libraries while building the image. Last step is to copy the R scripts and any data files in the folder into the container. Here I am copy ing all teh contents to `/work` directory in the container
```
RUN Rscript --vanilla -e "install.packages(c('shiny', 'dplyr', 'leaflet'), repos = 'http://cran.us.r-project.org');"
RUN mkdir /work
ADD RProj /work
```

Finally Build and run the container:
```
docker build -t my-rshiny:test .
docker run -it -p 5000:5000 my-rshiny:test Rscript --vanilla /work/script.R
```


## How to run this on Azure?
1.	Make sure you have pushed the container to docker hub or ACR
2.	Use `az login` from the azure CLI to logon to azure.
3.	Create a resource group for this test: `az group create --name myshiny-app-res --location westus`
4.	Create a container on ACI, container name here is myshiny-app1: `az container create --image docker.io/<docker_id>/<image_name>:latest --name myshiny-app1 --resource-group myshiny-app-res --ip-address public --port 5000 --command-line "Rscript --vanilla /work/script.R " --cpu 2 --memory 8`
5.	Wait for the container to be ready. Use this command to check the status: `az container show --name myshiny-app1 --resource-group myshiny-app-res`. Look for `"provisioningState": "Succeeded"` in the result of the `az container show` command. Finally, use the IP address and port in the result of `az container show` command to connect to the app. 
```
  "ipAddress": {
          "ip": "123.123.123.123",
          "ports": [
            {
              "port": 5000,
              "protocol": "TCP"
            }
          ]
        },
  "location": "westus",
  "name": "myshiny-app1",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myshiny-app-res",
  "restartPolicy": null,
  "state": "Running",
  "tags": null,
  "type": "Microsoft.ContainerInstance/containerGroups",
  "volumes": null
```


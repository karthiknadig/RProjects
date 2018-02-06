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

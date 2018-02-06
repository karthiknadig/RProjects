FROM ubuntu:16.04

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
 && apt-get install -y software-properties-common python-software-properties \
 && apt-get install -y apt-transport-https wget nginx \
 && rm -rf /var/lib/apt/lists/*

RUN apt-get update --fix-missing \
 && apt-get install -y libxml2-dev libssl-dev libcurl4-openssl-dev ed gtk2.0 build-essential libssh2-1-dev libgtk2.0-dev libiodbc2-dev xvfb python-dev \
 && rm -rf /var/lib/apt/lists/*

RUN sh -c 'echo "deb https://cran.revolutionanalytics.com/bin/linux/ubuntu xenial/" > /etc/apt/sources.list.d/cran-r.list' \
 && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E084DAB9 \
 && apt-get update \
 && apt-get install -y r-base-dev \
 && apt-get install -y r-cran-rodbc

RUN apt-get upgrade -y

RUN Rscript --vanilla -e "install.packages('devtools', repos = 'http://cran.us.r-project.org');"
RUN xvfb-run --server-args="-screen 0 1024x768x24" Rscript --vanilla -e "install.packages('cairoDevice', repos='http://cran.us.r-project.org')"
# RUN Rscript --vanilla -e "library(devtools);install_github('igraph/rigraph')" 

# Some packages need shiny like 'htmlwidgets'
RUN Rscript --vanilla -e "install.packages(c('shiny', 'dplyr', 'leaflet'), repos = 'http://cran.us.r-project.org');"

RUN mkdir /work
ADD RProj /work
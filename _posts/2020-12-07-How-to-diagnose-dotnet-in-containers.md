---
layout: default
title: "How to diagnose .net core process in kubernetes/docker"
date: 2022-12-07-00:00:00 -0000
comments: true
published: false
excerpt_separator: <!--more-->
---
**Login to a terminal in your container:** If you use kubernetes, make sure you have your kube .config is ready and deployed in ~/.kube/config. If you do use WSL with docker desktop, the /.kube/config file is shared within windows and WSL. 

List the pods withe below command
```bash
 kubectl get pods -n <target-namespace>
 ```
Then find the relevant container from the list and login to it with
```bash
kubectl exec --stdin --tty irma-ensemble-analytics-559dbc6547-g2kk8 -- /bin/bash
```

* **Download .net core sdk:** You need .net core sdk in in the container, so get the relevant from https://dotnet.microsoft.com/download/dotnet-core. 
You could use a command like below on linux shell (this is for .net core 3.1, use a different link for .net 5):
```bash
curl https://download.visualstudio.microsoft.com/download/pr/ec187f12-929e-4aa7-8abc-2f52e147af1d/56b0dbb5da1c191bff2c271fcd6e6394/dotnet-sdk-3.1.404-linux-x64.tar.gz --output dotnet-sdk-3.1.404-linux-x64.tar.gz
```
* **Extract and install .net core sdk:**
```bash
mkdir -p "$HOME/dotnet" && tar xzf dotnet-sdk-3.1.404-linux-x64.tar.gz -C "$HOME/dotnet"
export DOTNET_ROOT=$HOME/dotnet
export PATH=$PATH:$HOME/dotnet
```
* **Switch to dotnet folder:**
```bash
cd ~/dotnet
```
* **Install the tools:**
```bash
./dotnet tool install --global dotnet-counters
./dotnet tool install --global dotnet-trace
```
* **Add tools folder to path:**
```bash
export PATH="$PATH:/root/.dotnet/tools"
```

* **Observe the root process:**
```bash
dotnet-counters monitor -p 1
```


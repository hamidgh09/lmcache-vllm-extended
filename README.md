# lmcache-vllm-extended
This is the extended driver for LMCache to run in vLLM.

The repository contains basic code and templates for deploying the IK2221 course project.

## Required Repositories

To deploy your project, you need some other repositories that are already available on Github:

### 1. LMCache Engine ([Link][LMCache])

This repository contains the engine of LMCache for inferencing.
All the processing regarding the checks for KV-cache availability, and need for storing or retrieving chunks of data are happening here. 
The engine contains a (small) local storage to keep the KV-cache data on the same server.
This reduces the requirement for retrieving of data from the remote storage.

We use the ```v0.1.4-alpha``` version of LMCache in this project.

### 2. LMCache Server ([Link][LMCache-Server])

This repository contains all essential code for storing chunks of data (i.e., KVs) on a so called, remote server.
Ideally, this repository should be deployed on a separate server and allow multiple instances of LMCache engines to fetch and store data.
However, in this project we have only one LMCache instance and the server is co-located on the same server just for simplicity of deploying the system.

We use the ```v0.1.1-alpha``` version of LMCache server in this project.

### 3. LMCache vLLM Extended (Current Repository)

This repository contains vLLM injection parts required by LMCache.
In this project we extend this to include new APIs to serve user requests as well as a basic frontend to visualize user prompts and responses.

Most of your code should be implemented here unless asked otherwise in the project description.

## Setup Server

You have received a server with GPU and Ubuntu installed to run the basic setup of the system including all three repositories.
To do so, you need to clone all mentioned repositories and follow LMCache documentation to run the system. 

To make the process a bit easier for you, there is a bash script file ```project-setup.sh``` in this repository that automatically fetches the required repositories, creates a proper python virtual environment and installs the required packages. 
Please make sure the provided script file is executed correctly without any error in case you are using that. 
Additionally, you can read the provided script file and follow the setup process manually to ensure all requirements are fulfilled!

## How to Run

To run the project you need three separate terminals on the same machine. 
Please make sure that you have activated the virtual environment you made on the setup phase on all three terminals.

### 1. Run the LMCache Server

To run the LMCache storage server you can go to the ```lmcache-server``` directory and run:
```
python3 -m lmcache_server.server <server_ip> <port> <storage_dir>
```
The storage directory is where the server keeps all the stored KV-Caches.
Alternatively, it is possible to set `<sotrage_dir>` to `cpu` but in this project we prefer to have KV-caches written in a file.

### 2. Run the LMCache Engine

To run the LMCache engine you need to make sure the PYTHONPATH parameter is set correctly on your server with having `LMCache` and `lmcache-vllm-extended` correct directory in it.
To do so, and running the engine you can use the following commands:

```
export PYTHONPATH="<path_to_LMCache_repo>:<path_to_lmcache-vllm-extended>:$PYTHONPATH"
LMCACHE_CONFIG_FILE=<path_to_configuration_yaml> CUDA_VISIBLE_DEVICES=0 python lmcache_vllm/script.py serve Qwen/Qwen2.5-1.5B-Instruct --gpu-memory-utilization 0.8 --dtype half --port 8000
```

You can use the `configuration.yaml` file in this repository to start with running the project. More information about the configuration file and possible options are available at [LMCache documentation][LMCache-doc] website.

Indeed, you may need to change the `CUDA_VISIBLE_DEVICES` value to a proper number if you have more than one GPU on your machine and need to run the inferencing engine on another GPU than the first one.

### 3. Run the Frontend

There is a simple frontend provided in the `frontend` directory that you can go in and run:

```
streamlit run frontend.py
```

The provided frontend uses a sample text file and prepends it to all prompts sent in the browser.
You may need to modify that to achieve all requirements in the project description.

[LMCache]: https://github.com/LMCache/LMCache
[LMCache-Server]: https://github.com/LMCache/lmcache-server
[LMCache-doc]: https://docs.lmcache.ai/configuration/config.html

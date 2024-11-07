# cnasg-nims-comparison
Comparison with NIMs by CNASG
## Running Llama3-8b-Instruct NIM on-prem
### Some Reference Materials
https://developer.nvidia.com/blog/a-simple-guide-to-deploying-generative-ai-with-nvidia-nim/ 
https://docs.nvidia.com/nim/large-language-models/latest/profiles.html 
https://docs.nvidia.com/nim/large-language-models/latest/getting-started.html 
https://build.nvidia.com/meta/llama-3_1-8b-instruct?snippet_tab=Docker 
### Setting up NIM
Setup NGC Credentials
```docker login nvcr.io
Username: $oauthtoken
Password: <PASTE_API_KEY_HERE>
```
Setup API keys and cache folders for NIMs
```export NGC_API_KEY=bWJhZTNocmI1a2k0ZjBycDNzaGN0YjltYWY6NzM0YThmZGEtZjk3NC00OGI4LTkyZWMtOTcyOTQxOTY5MDM2
export LOCAL_NIM_CACHE=~/.cache/nim
mkdir -p "$LOCAL_NIM_CACHE"
```
Pull and run Llama-3.1-8b-Instruct NIMs Docker Container
```docker run -it -d --rm \
    --gpus device=0 \
    --shm-size=16GB \
    -e NGC_API_KEY=$NGC_API_KEY \
    -e CUDA_VISIBLE_DEVICES=0\
    -v "$LOCAL_NIM_CACHE:/opt/nim/.cache" \
    -u $(id -u) \
    -p 8000:8000 \
    --name nim-llm \
    nvcr.io/nim/meta/llama-3.1-8b-instruct:1.2.2```
Test to see if NIMs endpoint is reachable
```curl -X 'POST' \
'http://0.0.0.0:8000/v1/chat/completions' \
-H 'accept: application/json' \
-H 'Content-Type: application/json' \
-d '{
    "model": "meta/llama-3.1-8b-instruct",
    "messages": [{"role":"user", "content":"Write a limerick about the wonders of GPU computing."}],
    "max_tokens": 64
}'
```


### Setting up Environment
Start Jupyter Notebook
```docker run -it -d --rm --gpus device=1 --net host --name jupyter -v $PWD:/home/jovyan/work quay.io/jupyter/scipy-notebook:latest start-notebook.py --NotebookApp.token='password' --ip="0.0.0.0"
```
Go to: http://<YOUR-JUPYTER-VM-IP-ADDRESS>:8888 , for example http://172.27.193.230:8888 
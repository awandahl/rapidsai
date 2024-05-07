# rapidsai

TJ Dockerfile:
````
# Use the selected official CUDA development image
FROM nvidia/cuda:11.0.3-cudnn8-devel-ubuntu20.04

# Set noninteractive mode for apt-get
ENV DEBIAN_FRONTEND=noninteractive

# Install gnupg, curl, and other necessary tools before anything else
RUN apt-get update && apt-get install -y --no-install-recommends \
    gnupg2 \
    curl \
    ca-certificates && \
    rm -rf /var/lib/apt/lists/*

# Manually add NVIDIA GPG keys
RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/7fa2af80.pub && \
    apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/3bf863cc.pub

# Refresh package lists after adding keys
RUN apt-get update

# Install Python and pip
RUN apt-get install -y python3-pip python3-dev && \
    if [ ! -f /usr/bin/python ]; then ln -s /usr/bin/python3 /usr/bin/python; fi && \
    if [ ! -f /usr/bin/pip ]; then ln -s /usr/bin/pip3 /usr/bin/pip; fi

# Upgrade pip and install necessary Python packages
RUN pip install --upgrade pip && \
    pip install cupy-cuda110==9.3.0 nltk spacy notebook jupyterlab==4.1.1 && \
    python -m spacy download en_core_web_sm

# Set the working directory
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Expose Jupyter port
EXPOSE 8888

# Run Jupyter Notebook
CMD ["jupyter", "lab", "--ip='0.0.0.0'", "--port=8888", "--no-browser", "--allow-root"]


````
Building docker container:
````
docker build -t tj .
````
Running container detached and bind container to home directory
````
docker run --gpus all -d -p 8888:8888 -v /home/aw/docker/tj:/notebooks tj
````

Check token of running Jupyter Notebook server
````
docker logs 9dbd6b850b5c
````

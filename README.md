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

TJM Dockerfile:
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
    pip install nltk jupyterlab==4.1.1 && \
    pip install cupy-cuda110==9.3.0 && \
    pip install spacy && \
    python -m spacy download en_core_web_md && \
    pip install scispacy && \
    pip install transformers fasttext && \
    pip install xgboost lightgbm allennlp

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



ERROR: spacy 3.3.3 has requirement typer<0.5.0,>=0.3.0, but you'll have typer 0.9.4 which is incompatible.
ERROR: spacy 3.3.3 has requirement typing-extensions<4.6.0,>=3.7.4.1, but you'll have typing-extensions 4.11.0 which is incompatible.
ERROR: scispacy 0.5.4 has requirement spacy<3.8.0,>=3.7.0, but you'll have spacy 3.3.3 which is incompatible.
ERROR: en-core-web-md 3.7.1 has requirement spacy<3.8.0,>=3.7.2, but you'll have spacy 3.3.3 which is incompatible.
ERROR: botocore 1.34.99 has requirement urllib3<1.27,>=1.25.4; python_version < "3.10", but you'll have urllib3 2.2.1 which is incompatible.
ERROR: cached-path 1.1.6 has requirement huggingface-hub<0.11.0,>=0.8.1, but you'll have huggingface-hub 0.23.0 which is incompatible.








Saving the Docker Image Locally:
After successfully building your Docker image, tag it with a version or name that you want to save it as:
bash
Copy code
docker tag <image_id> your_username/image_name:tag
Replace <image_id> with the ID of your Docker image (you can find it using docker images), your_username with your Docker Hub username, image_name with the name you want to give to your image, and tag with a version tag (e.g., latest).
Save the tagged Docker image to a .tar file:
bash
Copy code
docker save -o image_name.tar your_username/image_name:tag
Replace image_name.tar with the desired filename for your saved Docker image.
Pushing the Docker Image to Docker Hub:
Log in to Docker Hub from the command line:
bash
Copy code
docker login
Enter your Docker Hub username and password when prompted.
Push the tagged Docker image to your Docker Hub repository:
bash
Copy code
docker push your_username/image_name:tag
This command will upload your Docker image to Docker Hub.

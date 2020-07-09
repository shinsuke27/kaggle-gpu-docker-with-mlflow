The following steps show you how to build a GPU-enabled environment on GCP.

## Create Compute Engine VM instance and ssh to it.

```
$ gcloud compute instances create kaggle-compute \
    --image-family=ubuntu-2004-lts \
    --image-project=ubuntu-os-cloud \
    --boot-disk-size="200" \
    --custom-cpu 24 \
    --custom-memory 22GB \
    --accelerator type=nvidia-tesla-t4,count=1 \
    --maintenance-policy="TERMINATE" \
    --no-restart-on-failure
$ gcloud compute ssh kaggle-compute
```

## Clone the repository and run set_up.sh

```
$ git clone https://github.com/shinsuke27/kaggle-gpu-docker-with-mlflow.git kaggle
$ sh ~/kaggle/set_up.sh
```

## Install CUDA Tooklit and ssh to the instance again.

Accept the EULA and install all items

```
$ wget http://developer.download.nvidia.com/compute/cuda/11.0.2/local_installers/cuda_11.0.2_450.51.05_linux.run
$ sudo sh cuda_11.0.2_450.51.05_linux.run
```

## Build official Kaggle Docker image and reboot.

```
$ git clone https://github.com/Kaggle/docker-python.git
$ cd docker-python
$ sudo ./build --gpu
$ sudo reboot
```

## Re-ssh

```
$ gcloud compute ssh kaggle-compute
```

## Build original Docker image from official.

```
$ cd ~/kaggle/
$ docker-compose up -d --build
```

## Re-ssh using ssh port forwarding

```
$ exit
$ gcloud compute ssh kaggle-compute -- -L 8080:localhost:8080 -L 5000:localhost:5000
```

## Access the container from the browser.

- Jupyter http://localhost:8080/
- MLflow http://localhost:5000/

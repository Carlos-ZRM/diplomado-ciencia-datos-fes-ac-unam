## Select image base

Kubeflow Documentación:

[https://www.kubeflow.org/docs/components/notebooks/container-images/](https://www.kubeflow.org/docs/components/notebooks/container-images/)

Kubeflow Ejemplo

[https://github.com/kubeflow/kubeflow/tree/master/components/example-notebook-servers/jupyter-pytorch-full](https://github.com/kubeflow/kubeflow/tree/master/components/example-notebook-servers/jupyter-pytorch-full)

~~~ Docker
#
# NOTE: Use the Makefiles to build this image correctly.
#

ARG BASE_IMG=<jupyter-pytorch>
FROM $BASE_IMG

# install - conda packages
# NOTE: we use mamba to speed things up
RUN mamba install -y -q \
    bokeh==3.6.3 \
    cloudpickle==3.1.1 \
    dill==0.3.9 \
    ipympl==0.9.6 \
    matplotlib==3.10.0 \
    numpy==1.26.4 \
    pandas==2.2.3 \
    scikit-image==0.25.1 \
    scikit-learn==1.6.1 \
    scipy==1.15.1 \
    seaborn==0.13.2 \
    xgboost==2.1.4 \
 && mamba clean -a -f -y

# install - requirements.txt
COPY --chown=${NB_USER}:${NB_GID} requirements.txt /tmp/requirements.txt
RUN python3 -m pip install -r /tmp/requirements.txt --quiet --no-cache-dir \
 && rm -f /tmp/requirements.txt
~~~

## Build image
~~~
podman build -t local-jupyter-pytorch-cuda:latest jupyter-v0

## Push to Registry
#podman push quay.io/rh-ee-creyesma/jupyter-pytorch-cuda:latest
~~~

## Run Python

~~~ bash
podman volume create jupyter
podman run --rm --name jupyter \
    -p 8888:8888 \
    -v jupyter:/home/jovyan:Z \
    quay.io/rh-ee-creyesma/jupyter-pytorch-cuda:latest
~~~

## Run RSTUDIO

~~~ bash
podman volume create rstudio
podman run --rm --name rstudio \
    -p 8888:8888 \
    -v rstudio:/home/jovyan:Z \
    ghcr.io/kubeflow/kubeflow/notebook-servers/rstudio:sha-6dbe516130193616f6304f0464c7d54699792a63-dirty
~~~

## Connect with VSCODE
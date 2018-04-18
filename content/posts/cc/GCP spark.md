---
title: "GCP for Spark"
date: "2018-03-27"
description: "This blog will introduce how to perform Spark job on GCP."
categories:
    - "Spark"
    - "Cloud computing"
    - "GCP"
---

GCP is very cheap compared with AWS and Azure. However, it did not have enough documentation and I met some problems that did not even on StackOverflow. That's why I want to write this blog.

# Start your cluster

We could use command line with GCP CLI to start cluster. 

```bash

gcloud dataproc clusters create cluster-name --project=project-id --bucket outputbucket --initialization-actions gs://xxx/jupyter.sh --master-machine-type=n1-standard-2 --worker-machine-type=n1-standard-1 --zone=xxxx

```

# Install external Python package on Spark

If we want to use your own package and Juypter NoteBook on Spark, you need to contain an initialization step for your cluster. 

** Please Noticed that you do not have permission to install python package after start your cluster! **

```bash

#!/usr/bin/env bash
set -e

ROLE=$(curl -f -s -H Metadata-Flavor:Google http://metadata/computeMetadata/v1/instance/attributes/dataproc-role)
INIT_ACTIONS_REPO=$(curl -f -s -H Metadata-Flavor:Google http://metadata/computeMetadata/v1/instance/attributes/INIT_ACTIONS_REPO || true)
INIT_ACTIONS_REPO="${INIT_ACTIONS_REPO:-https://github.com/GoogleCloudPlatform/dataproc-initialization-actions.git}"
INIT_ACTIONS_BRANCH=$(curl -f -s -H Metadata-Flavor:Google http://metadata/computeMetadata/v1/instance/attributes/INIT_ACTIONS_BRANCH || true)
INIT_ACTIONS_BRANCH="${INIT_ACTIONS_BRANCH:-master}"

JUPYTER_CONDA_CHANNELS=$(curl -f -s -H Metadata-Flavor:Google http://metadata/computeMetadata/v1/instance/attributes/JUPYTER_CONDA_CHANNELS || true)
JUPYTER_CONDA_PACKAGES=$(curl -f -s -H Metadata-Flavor:Google http://metadata/computeMetadata/v1/instance/attributes/JUPYTER_CONDA_PACKAGES || true)

echo "Cloning fresh dataproc-initialization-actions from repo $INIT_ACTIONS_REPO and branch $INIT_ACTIONS_BRANCH..."
git clone -b "$INIT_ACTIONS_BRANCH" --single-branch $INIT_ACTIONS_REPO
./dataproc-initialization-actions/conda/bootstrap-conda.sh

source /etc/profile.d/conda.sh

if [ -n "${JUPYTER_CONDA_CHANNELS}" ]; then
  echo "Adding custom conda channels '$(echo ${JUPYTER_CONDA_CHANNELS} | tr ':' ' ')'"
  conda config --add channels $(echo ${JUPYTER_CONDA_CHANNELS} | tr ':' ',')
fi

if [ -n "${JUPYTER_CONDA_PACKAGES}" ]; then
  echo "Installing custom conda packages '$(echo ${JUPYTER_CONDA_PACKAGES} | tr ':' ' ')'"
  conda install $(echo ${JUPYTER_CONDA_PACKAGES} | tr ':' ' ')
fi

if [[ "${ROLE}" == 'Master' ]]; then
    conda install jupyter
    conda install xxxxx  # install your package here!!
    pip install jgscm==0.1.7

    ./dataproc-initialization-actions/jupyter/internal/setup-jupyter-kernel.sh
    ./dataproc-initialization-actions/jupyter/internal/launch-jupyter-kernel.sh
fi
if [[ "${ROLE}" == 'Worker' ]]; then
    conda install xxxx # install your package here!!
    pip install jgscm==0.1.7
fi
echo "Completed installing Jupyter!"

if [[ ! -v $INSTALL_JUPYTER_EXT ]]
    then
    INSTALL_JUPYTER_EXT=false
fi
if [[ "$INSTALL_JUPYTER_EXT" = true ]]
then
    echo "Installing Jupyter Notebook extensions..."
    ./dataproc-initialization-actions/jupyter/internal/bootstrap-jupyter-ext.sh
    echo "Jupyter Notebook extensions installed!"
fi

```
# Begin your job

You could submit python file from your local machine.

```bash
gcloud beta dataproc jobs submit pyspark --cluster cluster-name python-file
```

You can also use Jupyter. You need to first build a ssh tunnel. You could find more information from [here](https://cloud.google.com/dataproc/docs/tutorials/jupyter-notebook)


---
title: Multi-Level Branched Regularization for Federated Learning
url: https://proceedings.mlr.press/v162/kim22a.html
labels: [data heterogeneity, knowledge distillation, image classification] 
dataset: [cifar100, tiny-imagenet] 
---

# *_FedMLB_*

> Note: If you use this baseline in your work, please remember to cite the original authors of the paper as well as the Flower paper.

****Paper:**** https://proceedings.mlr.press/v162/kim22a.html

****Authors:**** Jinkyu Kim, Geeho Kim, Bohyung Han

****Abstract:**** *_A critical challenge of federated learning is data
heterogeneity and imbalance across clients, which
leads to inconsistency between local networks and
unstable convergence of global models. To alleviate
the limitations, we propose a novel architectural
regularization technique that constructs
multiple auxiliary branches in each local model by
grafting local and global subnetworks at several
different levels and that learns the representations
of the main pathway in the local model congruent
to the auxiliary hybrid pathways via online
knowledge distillation. The proposed technique is
effective to robustify the global model even in the
non-iid setting and is applicable to various federated
learning frameworks conveniently without
incurring extra communication costs. We perform
comprehensive empirical studies and demonstrate
remarkable performance gains in terms of accuracy
and efficiency compared to existing methods.
The source code is available in our project page._*


## About this baseline

****What’s implemented:**** The code in this directory reproduces the results for FedMLB, FedAvg, and FedAvg+KD.
The reproduced results use the CIFAR-100 dataset. Four settings are available for CIFAR-100,
1. Moderate-scale with Dir(0.3), 100 clients, 5% participation, balanced dataset (500 examples per client).
2. Large-scale experiments with Dir(0.3), 500 clients, 2% participation rate, balanced dataset (100 examples per client).
3. Moderate-scale with Dir(0.6), 100 clients, 5% participation rate, balanced dataset (500 examples per client).
4. Large-scale experiments with Dir(0.6), 500 clients, 2% participation rate, balanced dataset (100 examples per client).


****Datasets:**** CIFAR-100, Tiny-ImageNet.

****Hardware Setup:**** The code in this repository has been tested on a Linux machine with 64GB RAM. 
Be aware that in the default config the memory usage can exceed 10GB.

****Contributors:**** Alessio Mora (University of Bologna, PhD, alessio.mora@unibo.it).

## Experimental Setup

****Task:**** Image classification

****Model:**** ResNet-18.

****Dataset:**** Four settings are available for CIFAR-100,
1. Moderate-scale with Dir(0.3), 100 clients, 5% participation, balanced dataset (500 examples per client).
2. Large-scale experiments with Dir(0.3), 500 clients, 2% participation rate, balanced dataset (100 examples per client).
3. Moderate-scale with Dir(0.6), 100 clients, 5% participation rate, balanced dataset (500 examples per client).
4. Large-scale experiments with Dir(0.6), 500 clients, 2% participation rate, balanced dataset (100 examples per client).

****Dataset:**** Four settings are available for Tiny-Imagenet,
1. Moderate-scale with Dir(0.3), 100 clients, 5% participation, balanced dataset (1000 examples per client).
2. Large-scale experiments with Dir(0.3), 500 clients, 2% participation rate, balanced dataset (200 examples per client).
3. Moderate-scale with Dir(0.6), 100 clients, 5% participation rate, balanced dataset (1000 examples per client).
4. Large-scale experiments with Dir(0.6), 500 clients, 2% participation rate, balanced dataset (200 examples per client).

****Training Hyperparameters:**** 

| Hyperparameter  | Description | Default Value |
| ------------- | ------------- | ------------- |
| client optimizer  | Local optimizer. |  SGD|
| client learning rate  |  |  0.1 |
| learning rate decay  | Exponential decay rate for clients' learning rate. |  0.998 |
| server optimizer  | Server optimizer. (SGD with lr=1 is equivalent to applying updated by sum). |  SGD|
| server learning rate  |  |  1.0 |
| clip norm  | Clip norm during local training. | 10.0 |
| weight decay  | Weight decay during local training. |  1e-3 |
| $\lambda_1$  | Used in FedMLB. See Eq. 8 in the original paper. It weights the impact of hybrid cross-entropy loss.|  1.0 |
| $\lambda_2$  | Used in FedMLB. See Eq. 8 in the original paper. It weights the impact of hybrid KL loss.| 1.0 |
| temperature  | Regulates the smoothness of local and global predictions in distillation. | 1.0 |
| $\gamma$   | Used in FedAvg+KD. It weights the impact of local-global distillation on local training.| 0.2 |

## Environment Setup
By default, Poetry will use the Python version in your system. 
In some settings, you might want to specify a particular version of Python 
to use inside your Poetry environment. You can do so with `pyenv`. 
Check the documentation for the different ways of installing `pyenv`,
but one easy way is using the automatic installer:

```bash
curl https://pyenv.run | bash
```
You can then install any Python version with `pyenv install <python-version>`
(e.g. `pyenv install 3.9.17`) and set that version as the one to be used. 
```bash
# cd to your FedMLB directory (i.e. where the `pyproject.toml` is)
pyenv install 3.9.0

pyenv local 3.9.0

# set that version for poetry
poetry env use 3.9.0
```
To build the Python environment as specified in the `pyproject.toml`, use the following commands:
```bash
# cd to your FedMLB directory (i.e. where the `pyproject.toml` is)

# install the base Poetry environment
poetry install

# activate the environment
poetry shell
```
## Running the Experiments
Ensure you have activated your Poetry environment (execute `poetry shell` from
this directory).

### Generating clients' dataset
First (and just the first time), the data partitions of clients must be generated.

#### Default config and custom config for clients' dataset generation (CIFAR-100)

To generate the partitions for the CIFAR-100 settings, i.e.:
1. Moderate-scale with Dir(0.3), 100 clients, balanced dataset (500 examples per client) -- **default config**.
2. Large-scale experiments with Dir(0.3), 500 clients, balanced dataset (100 examples per client);
3. Moderate-scale with Dir(0.6), 100 clients, balanced dataset (500 examples per client);
4. Large-scale experiments with Dir(0.6), 500 clients, balanced dataset (100 examples per client),

use the following commands:
```bash
# this will run using the default settings in the `conf/base.yaml`
# and will generate the setting for 1. (see above)
python -m FedMLB.dataset_preparation 

# this will generate the setting for 2. (see above)
python -m FedMLB.dataset_preparation dataset_config.alpha_dirichlet=0.3 total_clients=500

# this will generate the setting for 3. (see above)
python -m FedMLB.dataset_preparation dataset_config.alpha_dirichlet=0.6 

# this will generate the setting for 4. (see above)
python -m FedMLB.dataset_preparation dataset_config.alpha_dirichlet=0.6 total_clients=500
```
Note that, to reproduce those settings, we leverage the .txt files
contained in the`client_data` folder in this project. Such files store
the specific id of examples in the dataset that are associated with a specific client. 
For example, the file `client_data/cifar100/balanced/dirichlet0.3_clients100.txt` contains the
examples for the default setting of this repository. Note that those files are provided by the authors themselves.

#### Custom config for clients' dataset generation (Tiny-ImageNet)
1. Moderate-scale with Dir(0.3), 100 clients, balanced dataset (1000 examples per client).
2. Large-scale experiments with Dir(0.3), 500 clients, balanced dataset (200 examples per client).
3. Moderate-scale with Dir(0.6), 100 clients, balanced dataset (1000 examples per client).
4. Large-scale experiments with Dir(0.6), 500 clients, balanced dataset (200 examples per client).

> Note: To generate the clients' dataset for the Tiny-Imagenet, the original dataset should be downloaded in advance.\
> It can be downloaded at http://cs231n.stanford.edu/tiny-imagenet-200.zip. Unzip the folder. \
> Note: This code supposes to find the folder at the path `/{YOUR_LOCAL_PATH_TO_THE_BASELINE}/FedMLB/tiny-imagenet-200`.

> :warning:
For Tiny-ImageNet, ensure that the unzipped folder is correctly located at `/{YOUR_LOCAL_PATH_TO_THE_BASELINE}/FedMLB/tiny-imagenet-200`.
The `tiny-imagenet-200` folder contains three folders (`train`, `val`, `test`) and two `.txt` files.

```bash
# commands to generate clients' dataset partitions with Tiny-imagenet
# this will generate the setting for 1. (see above)
python -m FedMLB.dataset_preparation dataset_config.dataset="tiny-imagenet" 

# this will generate the setting for 2. (see above)
python -m FedMLB.dataset_preparation dataset_config.dataset="tiny-imagenet" total_clients=500

# this will generate the setting for 3. (see above)
python -m FedMLB.dataset_preparation dataset_config.dataset="tiny-imagenet" dataset_config.alpha_dirichlet=0.6 

# this will generate the setting for 4. (see above)
python -m FedMLB.dataset_preparation dataset_config.dataset="tiny-imagenet" dataset_config.alpha_dirichlet=0.6 total_clients=500
```

### Using GPUs
The code in this repository relies on TF library.
To make the simulations run on GPUs use the option `client_resources.num_cpus={PER_CLIENT_FRACTION_OF_GPU_MEMORY}`.
The default is `num_cpus=0.0`. 
For example, the following command will run on CPU only. 
```bash
python -m FedMLB.main # `client_resources.num_gpus=0.0` default
```

> :warning:
Ensure that TensorFlow is configured to use GPUs.


### Run simulations and reproduce results
After having generated the setting, simulations can be run.
 
#### CIFAR-100
The default configuration for `FedMLB.main` uses (1.) for CIFAR-100, and can be run with the following:

```bash
python -m FedMLB.main # this will run using the default settings in the `conf/base.yaml`

# you can override settings directly from the command line
python -m FedMLB.main clients_per_round=10 # this will run using 10 clients per round instead of 5 clients as the default config 

# this will select the dataset partitioned with 0.6 concentration paramater instead of 0.3 as the default config
python -m FedMLB.main dataset_config.alpha_dirichlet=0.6
```

To run using FedAvg:
```bash
# this will use the regular FedAvg local training
python -m FedMLB.main algorithm="FedAvg"
```

To run using FedAvg+KD:
```bash
# this will use the regular FedAvg local training
python -m FedMLB.main algorithm="FedAvg+KD"
```

#### Tiny-Imagenet
For Tiny-ImageNet, as in the orginal paper, batch size of local updates should be set 
to 100 in settings with 100 clients and to 20 in settings with 500 clients;
this is equal to set the amount of local_updates to 50 (as the default), since 
local_updates = (num_of_local_examples*local_epochs)/batch_size.

```bash
python -m FedMLB.main dataset_config.dataset="tiny-imagenet" 

python -m FedMLB.main dataset_config.dataset="tiny-imagenet" total_clients=500 clients_per_round=10

python -m FedMLB.main dataset_config.dataset="tiny-imagenet" dataset_config.alpha_dirichlet=0.6  

python -m FedMLB.main dataset_config.dataset="tiny-imagenet" dataset_config.alpha_dirichlet=0.6 total_clients=500 clients_per_round=10
```

## Expected Results
This repository can reproduce the results for 3 baselines used in the experimental part
of the original paper: FedMLB, FedAvg, FedAvg+KD.

The following tables compares results produced via the code in this repository
with the results reported in the paper.
Results from the paper are reported in brackets. 
Note that (as in the original paper),
the accuracy at the target round is based on the exponential moving average with the momentum parameter
0.9. Tensorboard allows to easily visualize/calculate the moving average of a run.

### Table 1a

The results of Table 1a in the paper (for FedAvg and FedMLB) refers
to CIFAR-100 (Figure 3) and Tiny-ImageNet (Figure 7a) with 
the setting (1.) Moderate-scale with Dir(0.3), 100 clients, 5% participation rate.

To reproduce the results run the following:

```bash
# this will produce six consecutive runs
python -m FedMLB.main --multirun dataset_config.dataset="cifar100","tiny-imagenet" algorithm="FedMLB","FedAvg","FedAvg+KD" 
```
#### CIFAR-100, Dir(0.3), 100 clients, 5% participation.

<table>
  <tr>
    <td></td>
    <td colspan="2">Accuracy @500R</td>
    <td colspan="2">Accuracy @1000R</td>
  </tr>
  <tr> 
    <td>Method</td>
    <td>Paper</td>
    <td>This repo</td>
    <td>Paper</td>
    <td>This repo</td>
  </tr>
  <tr>
    <td>FedAvg</td>
    <td>41.88</td>
    <td>44.52</td>
    <td>47.83</td>
    <td>49.15</td>
  </tr>
  <tr>
    <td>FedAvg+KD</td>
    <td>42.99</td>
    <td>46.03</td>
    <td>49.17</td>
    <td>50.54</td>
  </tr>
  <tr>
    <td>FedMLB</td> 
    <td>47.39</td>
    <td>51.11</td>
    <td>54.58</td>
    <td>57.33</td>
  </tr>
</table>

#### Tiny-ImageNet, Dir(0.3), 100 clients, 5% participation.

<table>
  <tr>
    <td></td>
    <td colspan="2">Accuracy @500R</td>
    <td colspan="2">Accuracy @1000R</td>
  </tr>
  <tr>
    <td>Method</td>
    <td>Paper</td>
    <td>This repo</td>
    <td>Paper</td>
    <td>This repo</td>
  </tr>
  <tr>
    <td>FedAvg</td>
    <td>33.94</td>
    <td>33.39</td>
    <td>35.42</td>
    <td>35.78</td>
  </tr>
  <tr>
    <td>FedMLB</td>
    <td>37.20</td>
    <td>35.42</td>
    <td>40.16</td>
    <td>39.14</td>
  </tr>
</table>

### Table 1b

The results of Table 1a in the paper (for FedAvg and FedMLB) refers
to CIFAR-100 (Figure 3) and Tiny-ImageNet (Figure 7a) with 
the setting (2.) Large-scale experiments with Dir(0.3), 500 clients, 2% participation rate.

To reproduce the results run the following:
```bash
python -m FedMLB.main --multirun dataset_config.dataset="cifar100","tiny-imagenet" algorithm="FedMLB","FedAvg","FedAvg+KD" total_clients=500 clients_per_round=10
```
#### CIFAR-100, Dir(0.3), 500 clients, 2% participation.

<table>
  <tr>
    <td></td>
    <td colspan="2">Accuracy @500R</td>
    <td colspan="2">Accuracy @1000R</td>
  </tr>
  <tr>
    <td>Method</td>
    <td>Paper</td>
    <td>This repo</td>
    <td>Paper</td>
    <td>This repo</td>
  </tr>
  <tr>
    <td>FedAvg</td>
    <td>29.87</td>
    <td>32.54</td>
    <td>37.48</td>
    <td>38.75</td>
  </tr>
  <tr>
    <td>FedMLB</td>
    <td>32.03</td>
    <td>36.63</td>
    <td>42.61</td>
    <td>46.65</td>
  </tr>
</table>

#### Tiny-ImageNet, Dir(0.3), 500 clients, 2% participation.


<table>
  <tr>
    <td></td>
    <td colspan="2">Accuracy @500R</td>
    <td colspan="2">Accuracy @1000R</td>
  </tr>
  <tr>
    <td>Method</td>
    <td>Paper</td>
    <td>This repo</td>
    <td>Paper</td>
    <td>This repo</td>
  </tr>
  <tr>
    <td>FedAvg</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>FedMLB</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


### Table 3

To reproduce results reported in Table 3 of the paper,
resulting from _more local iterations_ (K=100 or K=200 in the
paper, instead of K=50), run the following:
```bash
python -m FedMLB.main --multirun algorithm="FedMLB","FedAvg","FedAvg+KD" local_updates=100 # or local_updates=200 
```

## Results logging via Tensorboard
Beside storing results in plain text in the `output` folder, the results are also stored via 
tensorboard logs.

To launch the tensorboard to monitor results:
```bash
tensorboard --logdir /{YOUR_LOCAL_PATH_TO_THE_BASELINE}]/FedMLB/FedMLB/tb_logging/
```
The command will output an address for localhost,
and results can be navigated and visualized via tensorboard GUI 
by using a browser at that address. 

Tensorboard logs in this baselines are stored in a folder with the following path structure: 
`FedMLB/FedMLB/tb_logging/{DATASET}/{MODEL}/{METHOD}/{TOTAL_CLIENTS}_clients/dir_{ALPHA_DIRICHLET}/seed_{RANDOM_SEED}`

For example, for default results with FedAvg, logs will be stored at:
`FedMLB/FedMLB/tb_logging/cifar100/resnet18/FedAvg/100_clients/dir_0.3/seed_3`
 
## Additional Detail About This Implementation
The `models.py` file contains the implementation of the models used in this baseline. A regular ResNet18 is
implemented for regular FedAvg. However, FedMLB leverages *hybrid paths* to regularize local training,
with such hybrid paths composed of a series of frozen blocks from the global models (see the paper
for reference). As in the original implementation, in `models.py` you will find the `ResNet18MLB`
that is a custom version of the regular ResNet18 containing the support for such hybrid paths (see the
`call` method of `ResNet18MLB` class).

This baseline implementation is based on TensorFlow and Keras primitives. Note that the custom
local training of `FedMLB` and `FedAvg+KD` is implemented by subclassing the `tf.keras.Model` class 
and overriding the `train_step` method (which is called automatically at each batch iteration). Those 
custom `tf.keras.Model` are contained in `FedAvgKDModel.py` and `FedMLBModel.py`.

Note that `FedMLB` and `FedAvg+KD` are client-side methods, which do not impact the aggregation phase 
of regular FedAvg (so this means that the built-in Flower strategy for FedAvg does not require
modifications). For this reason, there is no code in `server.py`.

To reproduce results, preprocessing of data is of paramount importance. 
For CIFAR-100, the preprocessing of train images includes normalization,
random rotation, random crop and random flip of images; test images
are normalized. 
For Tiny-ImageNet, the preprocessing of train images includes normalization,
random rotation, random crop and random flip of images; test images
are normalized and center cropped.
This code uses or defines subclasses of the `tf.keras.layers.Layer` class
to leverage a series of preprocessing layer to be used in the `.map()`
primitive of `tf.data.Dataset` (see `dataset.py`).

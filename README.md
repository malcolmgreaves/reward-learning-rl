# reward-learning-rl

This repository is the official implementation of the following paper: 

**End-to-End Robotic Reinforcement Learning without Reward Engineering** <br/>
Avi Singh, Larry Yang, Kristian Hartikainen, Chelsea Finn, Sergey Levine <br/>
arXiv 2019 <br/>
[Website](https://sites.google.com/view/reward-learning-rl/) | [Video](https://www.youtube.com/watch?v=9pWJzb4G-CA) | [Arxiv](todo)

| Visual Draping             |  Visual Pushing | Visual Bookshelf | 
:-------------------------:|:-------------------------:|:-------------------------:
![](https://lh5.googleusercontent.com/LijlWmUtupIz-NHFK0_EpRFnLYIfXBu-N2MckhLOAr9YYbi5onOLrXddSUlweyCUMuSQa_AEcOBNbgWNH3HFLXF1Rmh7TXi_qSMd2i0S8YSxffxyq8k=w371)  |  ![](https://lh4.googleusercontent.com/J-ltHmdkasD_F6Y1_9v_gqMqPem0XhImWQuFaCScdWn0hiNooqHhotRSQ4dLIhm2cmZlbYICffN0c05hoqn2oNzbiNtS2gI7LFhSCTlTQ6ARS_OpMrE=w371) |  ![](https://lh4.googleusercontent.com/Drp8HHXCp3ITy47iLOdoT0Bd9XrWQ1x_XtLgBRzl090Yso9bzuRH2DtpycCBFKPnzB_ySaI6hbnuoUU3reM7-cVBPUfKWNf2PQvdtWrEoqhlTtCCceA=w371) <br/> ![](https://lh4.googleusercontent.com/Drp8HHXCp3ITy47iLOdoT0Bd9XrWQ1x_XtLgBRzl090Yso9bzuRH2DtpycCBFKPnzB_ySaI6hbnuoUU3reM7-cVBPUfKWNf2PQvdtWrEoqhlTtCCceA=w371)


| Visual Door Opening      |   Visual Pusher | Visual Picker | 
:-------------------------:|:-------------------------:|:-------------------------:
![](https://lh5.googleusercontent.com/p2Wlgsie_ZwLh-D2AZxcGd2U9bR8XWDpSNxqeuBNKN25PI4xqkEZEBW-qnB2CzZLsfrmcqA=w371) | ![](https://lh6.googleusercontent.com/giwZbwKHi4xhLXxBWUTHbi1bx1e1PMvqfOYA5_4TohxfLfQztAdEBiVufyotDOQ66gSkpvRQ9g=w371) | ![](https://lh6.googleusercontent.com/LA_QZaFuy2Wq4V55kx9hHR4vfGzk3bMMV2tIj1h9T3pjy_QhKLYmDHugzGfDhdQ-6YQhGgc=w371) |

We propose a method for end-to-end learning of robotic skills in the real world using deep reinforcement learning. We learn these policies directly on pixel observations, and we do so without any hand-engineered or task-specific reward functions, and instead learn the rewards for such tasks from a small number of user-provided goal examples (around 80), followed by a modest number of active queries (around 25-75).  

This implementation is based on [softlearning](https://github.com/rail-berkeley/softlearning).

# Getting Started

## Prerequisites

The environment can be run either locally using conda or inside a docker container. For conda installation, you need to have [Conda](https://conda.io/docs/user-guide/install/index.html) installed. For docker installation you will need to have [Docker](https://docs.docker.com/engine/installation/) and [Docker Compose](https://docs.docker.com/compose/install/) installed. Also, most of our environments currently require a [MuJoCo](https://www.roboti.us/license.html) license.

## Conda Installation

1. [Download](https://www.roboti.us/index.html) and install MuJoCo 1.50 from the MuJoCo website. We assume that the MuJoCo files are extracted to the default location (`~/.mujoco/mjpro150`).

2. Copy your MuJoCo license key (mjkey.txt) to ~/.mujoco/mjkey.txt:

3. Clone `softlearning`
```
git clone https://github.com/rail-berkeley/softlearning.git ${SOFTLEARNING_PATH}
```

4. Create and activate conda environment, install softlearning to enable command line interface.
```
cd ${SOFTLEARNING_PATH}
conda env create -f environment.yml
conda activate softlearning
pip install -e ${SOFTLEARNING_PATH}
```

The environment should be ready to run. See examples section for examples of how to train and simulate the agents.

Finally, to deactivate and remove the conda environment:
```
conda deactivate
conda remove --name softlearning --all
```

## Docker Installation

### docker-compose
To build the image and run the container:
```
export MJKEY="$(cat ~/.mujoco/mjkey.txt)" \
    && docker-compose \
        -f ./docker/docker-compose.dev.cpu.yml \
        up \
        -d \
        --force-recreate
```

You can access the container with the typical Docker [exec](https://docs.docker.com/engine/reference/commandline/exec/)-command, i.e.

```
docker exec -it softlearning bash
```

See examples section for examples of how to train and simulate the agents.

Finally, to clean up the docker setup:
```
docker-compose \
    -f ./docker/docker-compose.dev.cpu.yml \
    down \
    --rmi all \
    --volumes
```

## Examples
### Training an agent
```
softlearning run_example_local examples.classifier_rl \
--n_goal_examples 10 \
--task=Image48SawyerDoorPullHookEnv-v0 \
--algorithm VICERAQ \
--num-samples 5 \
--n_epochs 300 \
--active_query_frequency 10
```
The tasks used in the paper were `Image48SawyerPushForwardEnv-v0`, `Image48SawyerDoorPullHookEnv-v0` and `Image48SawyerPickAndPlace3DEnv-v0`.  For the algorithm, you can experiments with `VICERAQ`, `VICE`,  `RAQ`, `SACClassifier`, and `SAC`. The `num-sample` flag specifies the number of random seeds launched. All results in the paper were averaged across five random seeds. The hyperparameters are stored in `examples/classifier_rl/variants.py`. 

`examples.classifier_rl.main` contains several different environments. For more information about the agents and configurations, run the scripts with `--help` flag: `python ./examples/classifier_rl/main.py --help`. 

If this codebase helps you in your academic research, you are encouraged to cite our paper. Here is an example bibtex:
```
@article{singh2019,
  title={End-to-End Robotic Reinforcement Learning without Reward Engineering},
  author={Avi Singh, Larry Yang, Kristian Hartikainen, Chelsea Finn, and Sergey Levine},
  journal={arXiv preprint arXiv:1904.TODO},
  year={2019}
}
```

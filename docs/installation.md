---
sidebar_position: 2
---

# Installation

OpenVIMO has two depedencies:

- The [Covfee framework](https://josedvq.github.io/covfee/docs) which takes care of experiment management. Covfee is a Python package and server that will read a study specification coded in Python and generate web pages and URLs that can be sent to the study participants.
- The [OpenVIDU framework](https://docs.openvidu.io/en/stable/getting-started/) which takes care of video communication and recording. For our purposes OpenVIDU is a video communication server that will run alongside the Covfee server and will take care of WebRTC communication specifically. Covfee will communicate with the OpenVIDU server under the hood.

:::warning
This tutorial assumes you have administrator rights on your machine.
:::

### Requirements

- Install [Docker](https://docs.docker.com/engine/install/)
- Make sure to have access to Python (ideally 3.10). We recommend conda or another way of creating virtual environments.

### Covfee setup

Set Covfee up for local development:

1. Create a virtual environment for covfee. We recommend a conda environment:

```text
conda create --name covfee python=3.10
conda activate covfee
```

2. Install covfee:

```text
git clone https://github.com/josedvq/covfee.git
cd covfee
python -m pip install -e .
scripts/dev-setup.sh
```

3. Make sure it works. Covfee includes [sample projects](https://github.com/josedvq/covfee/tree/master/samples). Try running one:

```bash
# Edit covfee/dev/dev.env and set PROJECT_DIR=./samples/basic
covfee-dev run
```

### OpenVidu setup

1. Run OpenVIDU

```text
docker run -p 4443:4443 --rm -e OPENVIDU_SECRET=MY_SECRET openvidu/openvidu-dev:2.30.0
```

Check the list of [OpenVidu options](https://docs.openvidu.io/en/stable/reference-docs/openvidu-config/) for more.

2. Test that OpenVIDU calls work:

Log in to the OpenVidu Dashboard using the URL displayed when running with Docker. Use credentials `OPENVIDUAPP` for username and the `$OPENVIDU_SECRET` you specified when starting the Docker container as password.

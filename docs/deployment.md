---
sidebar_position: 5
---

# Deploying online

## OpenVIDU

We recommend deploying and testing OpenVIDU first as it has the most requirements.

Particularly, OpenVIDU [requires several open ports](https://docs.openvidu.io/en/stable/deployment/ce/on-premises/#1-prerequisites). Opening these ports in a university or company network will likely require approval from your network administrator.

Follow the [on-premise deployment instructions](https://docs.openvidu.io/en/stable/deployment/ce/on-premises/)

Make sure to test OpenVIDU by logging into its test application. Log in to the OpenVidu Dashboard using the URL displayed when running with Docker. Use credentials `OPENVIDUAPP` for username and the `$OPENVIDU_SECRET` as password.

## OpenVIMO

Move your covfee app to production by following Covfee's [deployment instructions](https://josedvq.github.io/covfee/docs/deployment).

## Production Docker

The starter repositories now include split deploy stacks:

- a backend service
- a standalone Node Redux store service
- an nginx fileserver for the mounted `www/` folder

For OpenVIMO-based projects, deploy OpenVidu separately in production and then run only the `covfee`, `store`, and `www` services from the project repository.

# Deploying a Gradio App on IBM Cloud Code Engine

A simple Gradio app containerized with Docker and deployed as a serverless application on IBM Cloud Code Engine.

## Files
- demo.py: Gradio app
- requirements.txt: Python dependencies
- Dockerfile: container image definition

## Run locally with Docker
    docker build -t demo .
    docker run -p 7860:7860 demo

## Deploy to Code Engine
    ibmcloud ce build create ...
    ibmcloud ce app create --name demo1 --image <registry-image> --port 7860 --minscale 1

Built as part of the IBM Skills Network lab.

---
title: Speech Assistant with OpenAI real-time API and Twilio phone
date: 2024-11-03 12:00 -500
categories: [Architecture,LLM,GenAI,WebSockets,RealTime,Voice,Audio]
tags: [GenAI, LLM, OpenAI, Voice, Audio]
author: gowda
---

## Introduction
Using Voice or Speech assistant, you can talk to OpenAI real-time API and it talks back to you. Here you can use your phone/web and talk to it in real-time.

Real time API works on WebSockets protocol to connect to end users. 

### Prerequisites
    - Python 3.9+
    - A Twilio account for Phone
    - OpenAI API Key --> Pay as you go model
    - A ngrok account - Ngrok is a tunneling service that allows you to expose local servers to the internet through a secure URL. This is required if you are testing the code running locally (against hosting it in hosting platform/cloud)

## Architecture

![Desktop View](/assets/img/voice-assistant.png)

### Local setup of code
1. clone the code from this repo
    * <https://github.com/rameshagowda/voice-audio-assistant.git>
2. Update the env file with your OpenAI API key
3. Install the packages
4. run the code
<!--
#### Setup Ngrok tunneling solution which is similar to Azure Relay
1. Signup for Ngrok account free service to get the app up-and-running locally for development and testing. This is not required if you are deploying the code to Cloud or other hosting platforms like Replit. When developing & testing locally, you'll need to open a tunnel to forward requests to your local development server.
2. Install the Ngrok service on your machine as instructions provided. Also, follow the instructions to open the tunnel to your app running locally.
3. Once the tunnel has been opened, copy the Forwarding URL. It will look something like: https url with ngrok.app. You will need this when configuring your Twilio number setup.

#### Setup Twilio account and purchase a phone number 
1. Use the credits instead of paying for it as it is just for testing
2. In the Twilio Console, go to Phone Numbers > Manage > Active Numbers and click on the additional phone number you purchased for this app in the Prerequisites.
In your Phone Number configuration settings, update the first A call comes in dropdown to Webhook, and paste your ngrok forwarding URL, followed by /incoming-call. Then, click Save configuration.

## Test the App
Call the Twilio phone number from your phone to get started with talking to AI Speech Assistant.

## References:
* <https://www.youtube.com/watch?v=OVguB1h-eTs>
* <https://www.youtube.com/watch?v=xf6xT7RbTPo>
* <https://www.youtube.com/watch?v=b-DP0xPBwmI>

-->
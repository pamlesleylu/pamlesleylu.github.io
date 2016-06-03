---
layout: post
title: ChatOps at GitHub
tags: [hubot, chatbot]
---

Hubot in GitHub
hubot is campfire bot
evolved into primary control circuit

puppet for configuration management
use hubot interactive thing to deploy the chnage


flow: hubot deploy to production
1. puppet noop to try the configurations. hubot informs everyone of execution
2. puppet force deployment to a server. hubot shows output
3. graph me (using graphite) for the load of server in graph
4. merge pull. hubot informs about the pull request
4. ci runs. hubot informs you of steps
4. hubot puppet force production. hubot lets you know what happens
4. graph of everything
4. log to check the logs and send it to chat room (papertrail)

don't have to touch terminal
noone has to do something and tell someone else that they did it
no communication required between people and you can see the flow

hubot
works really well json api
everyone sees the build steps by hubot in campfire (a chatroom)

ChatOps
building tools that make it easier to operate infrastructure via hubot or terminal
place tools directly in the middlew of conversation, everyone is pairing all of the time
teaching by doing is awesome - side effect of putting everything in hubot - make all things visible

source:
[ChatOps in GitHub](https://www.youtube.com/watch?v=NST3u-GjjFw)

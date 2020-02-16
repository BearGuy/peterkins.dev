+++
draft = true
date = 2020-02-14
title = "Intro to Darklang - A Deployless Programmnig Language"
description = ""
+++

Outline:

  - Intro
    - What is Darklang?
    - Why is it important?
    - What problem does it solve?
    - How can I get started?
    - What is Trace Driven Development?

  - Building our First App
    - How do we write code?
      - What's a Canvas?
      - What tools do we have?
    - Setup our Discord bot
      - Create our app, get Bot token, etc.
      - Add our Webhook to our channel
    - Write our Code
      - We don't have WebHooks, we'll have to improvise
        - Let's create our `lastMessage` system to keep track
        - CRON job to fetch our latest messages
        - Post message to Channel on Keyword

  - Conclusion
    - Bugs and Current Issues
    - Potential of a "Deployless" Platform


# Intro

With the many tools that we have available in modern development, there are a bunch of processes and headaches one has to go to in order to deploy and host something on the internet. Not only do you have to knowledgable of today's app frameworks and architecture, you also need some sys admin skills to boot. While some of us have (by necessity) learned a lot of these skills over time, this still remains a large barier to entry for new developers, and developers who want to spend less time fiddling with configuration and more time shipping code.

There is more and more tooling available to address this problem (such as Netlify), however many of these solutions are only available for frontends. Hosting your REST API still takes a lot of time to manage and deploy.

## What is Darklang?

In comes [Darklang](https://darklang.com). Darklang provides a deployless backend. It's about an absence of complexity in a world where we have more tools than ever to build out our software.

Incrementalism is a disease, and it's what we do in software engineering!
No Silver Bullet --> accidental vs essential complexity

Dark is a new programming language with a holistic take on editing and deploying code. Pairing editor and infrastructure allows for a new computing paradigm that departs from the incrementalism that has consistently plagued software.

It provides essential backend primitives to write backend applications
- HTTP Endpoints
- CRON jobs
- Persistent Datastores
- Functions (Workers)
- REPL (for running and testing code)

Dark also uses an idea called "Trace Driven Development" where you can test and deploy your code simultanesouly, and see a trace of the application path in realtime!

## How Can I Get Started?
Currently the project is in private beta. You can sign up for the beta [here](https://darklang.com/beta), and if you have a specific project in mind that you want to build, you can reach out to Ellen and Paul and ask them to get you in asap



# Building our First App

Unfortunately, unless you're in the private beta, you won't be able to follow the steps of this tutorial.

The first thing we want to do is create a new `canvas`. A canvas is our visual environment that we'll be using to create our app.
We do this by typing in `https://darklang.com/a/USERNAME-CANVASNAME` and getting our blank canvas!

(provide screenshot for new canvas)

Awesome! Darklang is perfect for writing bots on other platforms. Let's create a Discord bot as an example.

Go to discord and

- Create our app
- get our bot token
- Creating a new server
- adding a webhook to our channel so we can post.

Ok, now that we have discord setup we can start to build out our Dark canvas.

First thing we want to do is create our CRON job that will be fetching out our latest messages. It might look something like this

(provide picture of CRON job)

Unfortunately, Dark does not have support for WebSockets just yet. As much of Discord's Gateway functionality is based around WebSockets, we need to find other ways to get our messages. Currently, hitting the Discord API every minute and scanning for new messages will have to do.

We make an api call to `/$channel_id/messages/` to get our messages, with an `after` flag set so we're not fetching all of the messages in the channel! Following that, we take the head of this new list and update our datastore with our `last_message` key to be the `message_id` of our latest Discord message.

(SHOW PICUTRE OF LAST MESSAGE DATASTORE)

WE'll want to manually update the `last_message`. Let's create a REPL to help do this for us

(SHOW LAST_MESSAGE REPL)

We can run this job manually hitting the `refresh` icon in the top right of our window, and we should see the "OK"

(SHOW PICUTRE OF OK)

Sick, looks like things are going gangbusters. Let's step this up and listen for a key word in our messages, and respond with a message of our own. Let's go with "!darkbot" and respond with a little wave and a link to Dark's website. We'll want to create a function that will post a message of our choice to this specific channel:

(Show picture of POST WINDOW)

Excellent, now let's hit the `refresh` and try it out. Over in our Discord channel, we'll see:

(SHOW MESSAGE IN DISCORD CHANNEL)

Cool, now let's bring it all together. Let's type in a `!darkbot` command to the channel and wait for our program to send a message in response. Becuase the limit to CRON job times is 1 minute, you might have to wait a second to see it. But we should be getting something like this:

(SHOW WHOLE PROCESS):

And there we have it, our Discrod bot has some legs!

# Conclusion

We've shown just a little bit about what coding in a "deployless" environment looks like, and the many advantages that come with it! This is just the beginning as well, I have confidence that the Dark team will continue to hone the performance and ability of the language as time goes on.

I'm sure many of you want to jump in and try it out yourselves, but like I mentioned earlier Dark is still in a closed beta. You can sign up for the beta [here](https://darklang.com/beta), and if you have a specific project in mind that you want to build, you can reach out to Ellen and Paul and ask them to get you in asap.

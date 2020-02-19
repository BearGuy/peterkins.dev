+++
date = 2020-02-14
title = "Let's Make a Discord Bot with Dark"
description = "The explosion of tooling in hosting software has created a crisis in deploying backend applications. Not only do you have to be knowledgeable of today's current app frameworks and architecture, you also need sysadmin skills to boot. By necessity developers have needed to learn these skills, and this complexity provides a steep learning curve for new developers."
+++

# What is Dark?
The explosion of tooling in hosting software has created a crisis in deploying backend applications. Not only do you have to be knowledgeable of today's current app frameworks and architecture, you also need sysadmin skills to boot. By necessity developers have needed to learn these skills, and this complexity provides a steep learning curve for new developers. Developers should spend less time fiddling with configuration and more time shipping code.

In comes [Dark](https://darklang.com). Dark is a new programming language with a holistic take on editing and deploying code. Currently it has a proprietary code editor, which ties very closely to the infrastructure that the code is running on. Pairing editor and infrastructure allows for a new computing paradigm that departs from the incrementalism that has consistently plagued software.

Dark provides essential primitives to write backend applications, which include:
- HTTP Endpoints
- CRON jobs
- Persistent Datastores
- Functions (Workers)
- REPL (for running and testing code)

A combination of these primitives will allow you to build 90% of backend of existing applications. Today, we'll showcase how to build a simple Discord Bot using these Dark tools.

## How Can I Get Started?
Currently the project is in private beta. You can sign up for the beta [here](https://darklang.com/beta), and if you have a specific project in mind that you want to build, you can reach out to Ellen and Paul and ask them to get you in asap.

# Building our First App

## Discord Setup
Assuming you already have a Discord account, we'll head to Discord's Developer Portal and click on the `New Application` button on the top right corner. Let's call the app "DiscordDemo". Click the "Create" button, and we have our app!

We'll want to add a bot to our `DarkDemo` app. We'll click on `Bot` tab on the left hand side under `Settings`. We'll be shown the bot page, where we'll click on `Add Bot` to create our bot. We'll get our bot, then give it `Administrator` privileges.

We'll want a test server to try this out. In Discord, we'll create a new server by clicking on `Add Server` button in our server list. Once we have our new server, we'll create a `dev` channel. We'll want to create a webhook so that we can post messages from Dark. We'll click on the `Settings` icon on the `dev` channel list item and get access to `Webhooks`. We'll click the `Create Webhook` button, and give it the name `DarkLang Demo Bot`.

## Writing our Dark Code
Ok, now that we have Discord infrastructure setup we can start writing our Dark code.

The first thing we want to do is create a new Dark `canvas`. A canvas is our visual environment that we'll be using to create our app. We do this by typing in `https://darklang.com/a/USERNAME-CANVASNAME` and getting our blank canvas! We'll create `https://darklang.com/a/bearguy-discord-bot`.

![](dark_blank_canvas.png)

There's three pieces of information that we'll want to retrieve from Discord. We want our
- Bot Token
- Channel ID
- Webhook URL

First thing we want to do is create our CRON job that will be fetching out our latest messages. It might look something like this:

![](darklang_basic_cron.png)

Unfortunately, Dark does not have support for websockets just yet. As much of Discord's App Gateway functionality is based around websockets, we need to find other ways to get our messages. Currently with Dark, we can run a CRON job to fetch new messages every minute. We'll make an api call to `https://discordapp.com/api/channels/$CHANNEL_ID/messages/` to get our messages using our `Bot token` as our authorization. We're checking to see if there are any messages that contain the keyword `!darklang`, and returning a boolean value.

We don't want to be checking all of the messages in the channel every call, so we want a way to only check messages past the latest message we've seen. Let's create a datastore for us to track the last message we've received from the API.

![DARK LAST MESSAGE DATASTORE](darklang_lastmessage_db.png)

We'll integrate this with our existing CRON job like so:

![DARK LAST MESSAGE INTEGRATED](darklang_cron_db.png)

Let's break this down. We're retrieving the latest message saved under the `lastid` key, and passing that as part of our HTTP request with `after` key in our request query. Following that, we take the head of this new list and update the `lastid` in our datastore with the `message.id` of our latest Discord message.

Right now, our program will fail because we won't be retrieving any message ids from our empty `LastID` datastore.
We'll want to manually update the `lastid`. Let's create a `REPL` to help do this for us. We can run this job manually hitting the `refresh` icon in the top right of our window, and we should see the `"Done!"`.

![]('darklang_lastid_repl.png')

Now we're getting somewhere! We're only retrieving new messages from our channel, and hunting for messages that match our keyword `!darklang`. Let's give it some code to execute when it finds a message that matches our criteria. We'll create a function called `postToChannel` that will post a message to our channel's webhook each time we find a matching message.

![]('darklang_post_function.png')

Excellent, now let's hit the `refresh` and try it out. Over in our Discord channel, we'll see:

![](discord_demo_message.png)

Cool, now let's bring it all together. We want to be triggering our `postToChannel` function when we detect our keyword in a message. Let's add it to our `checkForNewMessages` CRON job:

![](darklang_cron_with_function.png)


Let's type in a `!darkbot` command to the channel and wait for our program to send a message in response. Because the limit to the CRON job runtime is 1 minute, we might have to wait a second to see it. But we should be getting something like this:

![](discord_whole_process.png)

And there we have it, our Discord bot is operational!

# Conclusion

We've shown just a little bit about what coding in a "deployless" environment looks like, and the many advantages that come with it.This is just the beginning, I have confidence that the Dark team will continue to hone the performance and ability of the language as time goes on.

I'm sure many of you want to jump in and try it out yourselves, but like I mentioned earlier Dark is still in a closed beta. You can sign up for the beta [here](https://darklang.com/beta), and if you have a specific project in mind that you want to build, you can reach out to Ellen and Paul and ask them to get you in asap.

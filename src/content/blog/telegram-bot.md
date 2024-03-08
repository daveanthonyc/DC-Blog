---
title: 'How to Build a Telegram Bot in JavaScript and Seem Like a Magician'
description: 'Here I show you how create your own Telegram bot in JavaScript and how they provide value to the people around you'
pubDate: '9 March 2024'
heroImage: '/Telegram-Bot-Doprax-1.png'
---

Ok, take a look at this. Below is a Telegram message that contains attendance figures for 3 groups: O1, O2, O3. Individuals can attend online or offline sessions at several times slots.

***Now briefly think about how long it would take you to add up all the group's values manually...***

![Sample Attendance Form](/attendance.png)

Now if you're like me, mental maths isn't really a best friend.

However, leaders from the 3 groups constantly had to update their own group's attendance, which meant that someone would always have to re-calculate the totals over and over again. 
There were countless miscalculations and it always caused a bottle neck around how fast reports could be submitted. So I had the bright idea to make a bot that could do it automatically it ended up being adopted in several Telegram chats.

## Overview of Bot Tech Stack and Logic

This bot was created in JavaScript, Node.js and the Telegraf library and containerised in Docker and hosted in an AWS Ubuntu server.
It basically listens in to text messages that are sent to the Telegram chat. If the message is found to be the specific attendance form we are looking for, then we parse the string and extract the values of the attendance to produce a total and percentage. This is done by a series of string splits and string to number conversions.

And just like that, when I released the bot, it worked like absolute magic. All the manual calculations were gone and we instead get an instant total/percentage from the bot.

![Bot in action](/bot.gif)
![Reactions to the bot](/reactions.png)

I do love building things that save a lot of time and I'm sure you would also want to build a special internal tool that benefits your peers. So here's a tutorial on how to setup your own bot in JavaScript.

### How to setup your own Telegram bot
***Preliminary Steps***
* On Telegram search bar, type "***BotFather***" and access the chat.
* select the '***/newbot***' command and you will be given a prompt to give a name to your bot.
* You will then receive a token to access the HTTP API. Please store in a secure place as we will reference it.

1. Create a directory for your project 
```bash
mkdir chatbot
```
2. change directory to package
```bash
cd chatbot
```
3. Setup Node.js package
```bash
npm init
```
4. Install nodemon, telegraf
```bash
npm install nodemon telegraf dotenv
```
* ***[nodemon](https://www.npmjs.com/package/nodemon)*** allows for hot reload of your server making development faster
* ***[telegraf](https://github.com/telegraf/telegraf)*** will be the main library which provides a convenient API to access Telegram's services 

5. Configure nodemon in package.json. Add this "dev" script within the scripts object
```js
"scripts": {
    "dev": "nodemon index.js"
}
```
So when you run your development server, every time you save your project, it will automatically reload.
```bash
npm run dev
```

6. Create "index.js" & ".env" file in your root

7. Create ".env" file in root and add variable:
```
BOT_TOKEN="*Insert Telegram Token received previously"
```

8. Insert skeleton code into "index.js"
```javascript
/* IMPORTS */
import { Telegraf } from 'telegraf'
import { message } from 'telegraf/filters'
import dotenv, { parse } from 'dotenv';
dotenv.config();

/* CONFIG */
const bot = new Telegraf(process.env.BOT_TOKEN)

/* MAIN LOGIC OF BOT WHEN RECEIVE TEXT MESSAGE */
bot.on(message('text'), async (ctx) => {
    // access the string when the message comes in
    const text = ctx.message.text

    // add bot implementation here
    // .reply() sends argument back to the chat
    ctx.reply('some message'); 
})

bot.launch()

// Enable graceful stop
process.once('SIGINT', () => bot.stop('SIGINT'))
process.once('SIGTERM', () => bot.stop('SIGTERM'))

```

# Conclusion
You can find the repo for the Telegrm bot [here](https://github.com/daveanthonyc/Chatbot/tree/main).

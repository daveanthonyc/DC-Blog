---
title: 'How to Build a Telegram Bot in JavaScript'
description: 'Here I show you how create your own Telegram bot in JavaScript and how they provide value to the people around you'
pubDate: '9 March 2024'
heroImage: '/Telegram-Bot-Doprax-1.png'
---

Ok, take a look at this. Below is a Telegram message that contains attendance figures for 3 groups (O1, O2, O3) and there's several time slots someone can attend either offline/online.

***How long would it take you to add up all the group's values manually?...***

![Sample Attendance Form](/attendance.png)

Now if you're like me, mental maths isn't really a best friend.

But this form had to be updated constantly as people would update their own group's attendance, which meant that someone would always have to re-calculate the totals over and over again. 
I saw countless miscalculations (from myself included) and it always slowed down how fast reports could be submitted. So I had the bright idea to make a bot that could do it automatically and it ended up being adopted in several Telegram chats.

## Overview of Bot Tech Stack and Logic

This bot was created in JavaScript, Node.js and the Telegraf library and containerised in Docker and hosted in an AWS Ubuntu server.
It basically listens in to text messages that are sent to the Telegram chat. If the message is found to be the specific attendance form we are looking for, then we parse the string and extract the values of the attendance to produce a total and percentage. This is done by a series of string splits and string to number conversions.

You can check out the GitHub repo [here](https://github.com/daveanthonyc/Chatbot)

![Bot in action](/bot.gif)

And just like that, when I released the bot, it worked like absolute magic. All the manual calculations were gone and we instead get an instant total/percentage from the bot and people absolutely loved it because it solved a pain point.

![Reactions to the bot](/reactions.png)

***Little did I know that I had just created something that would be used throughout many chat rooms several times every week***.

I do love building things that save a lot of time and I'm sure you would also want to build a nice internal tool that benefits your peers. So here's a tutorial on how to setup your own Telegram bot in JavaScript.

---
<br/>

### How to setup your own Telegram bot
***We need to first create the Telegram bot and gain a token in order to access that bot and the incoming messages.***
* On Telegram search bar, type "***BotFather***" and access the chat.
* select the '***/newbot***' command and you will be given a prompt to give a name to your bot.
* You will then receive a token to access the HTTP API. Please store in a secure place as we will reference it.


***Once that is settled, we can now create the server that holds the logic of the bot***
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

6. Create `index.js` file in your root

7. Create `.env` file in root and add variable:
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

From here you can choose to insert any kind of logic based on the incoming text. The `ctx.message.text` object is the actual string that the server receives when someone messages in the same chat that the bot is in.
You can add a bunch of conditions to check if the message matches a particular form and then extract logic from it.

However, this is a great opportunity to implement the ***strategy design pattern***. I have been asked to make the bot parse different kind of forms as it became more popular, and I realised that it was a bit tedious to wrestle with the spaghetti code to fit in new logic as it wasn't following the Open/Close principle.

<br>

---
<br />

# Reflection
I realised that it is more natural to learn when you are faced with a problem that you want to solve badly. So what can help develop my skills is not just watching tutorials on the fundamentals for the sake of it, but finding more problems around me that require learning new technologies/fundamentals to solve it. 


For example, when I installed the node.js server on AWS, I was met with annoying dependency issues, so I just learnt docker quickly and packaged the app in it and it worked well.


Anyways, though the bot solved this little problem, there's a continuation to it. Yes it helped add the attendance figures, but someone has to then put those attendance figures into an excel for storage. Then someone has to ***create a report based on that excel***.


Hmmmmm, what if there was a way to just extract the data from Excel automatically and programatically create a visual report?

Check it out in the next article [Google Sheets as a Database for a Dashboard App?](/blog/google-sheets-db)

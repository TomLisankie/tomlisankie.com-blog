---
id: 5
title: Building a Chatbot with NativeScript, RiveScript, and Heroku
date: 2018-11-20T23:56:32+00:00
author: TomLisankie
layout: post
guid: http://tomlisankie.com/blog/?p=5
permalink: /2018/11/20/building-a-chatbot-with-nativescript-rivescript-and-heroku/
categories:
  - Tutorials
tags:
  - chatbot
  - heroku
  - mobile app
  - nativescript
  - rivescript
---
Getting a user to feel at home while using your app can be difficult. How can you design an interface that the user will already know how to use? There is one kind of interface that nearly every user knows how to interact with: a text conversation. Users already send text messages to their friends, use Facebook Messenger, Tinder, etc. So what better way to have a user immediately understand your app than building a conversational interface with a chatbot backend into your app? Thanks to RiveScript, building a chatbot doesn&#8217;t require any fancy AI techniques. Instead you can write a chatbot to give simple responses using near-plain text.

In this tutorial I&#8217;m going to teach you how to build a cross-platform chatbot app with NativeScript that uses RiveScript for the bot&#8217;s &#8220;brains.&#8221; The bot will live on a Heroku dyno. The application was initially modelled after [this](https://github.com/shiv19/nativescript-rivescript-demo). You can find the final code for this application over [here](https://github.com/TomLisankie/ChatbotApp) and the code for the backend [here](https://github.com/TomLisankie/ChatbotServer).

## Getting Started with Your First NativeScript App

First things first, before you start building a birdhouse you gotta get your tool belt out. If you don&#8217;t have the NativeScript Command Line Interface (CLI) installed, [do that first](https://docs.nativescript.org/start/quick-setup). Also, install the NativeScript Playground app on your [iOS](https://itunes.apple.com/us/app/nativescript-playground/id1263543946?mt=8&ls=1) or [Android](https://play.google.com/store/apps/details?id=org.nativescript.play) device. This is how you&#8217;ll be able to test the app as you&#8217;re building it.

Now that you have the tools set up, navigate to the directory you want to create your app in and type `tns create ChatbotApp` into your Terminal/Command Prompt and choose Plain TypeScript when prompted for which style you&#8217;d like. Then choose &#8220;Hello World&#8221; as the template. `tns` is the NativeScript command (it stands for &#8220;Telerik NativeScript&#8221; in case you were curious). `cd ChatbotApp` and open the directory in your text editor of choice. If you&#8217;re new to TypeScript (it&#8217;s just JavaScript with some strongly-typed bells and whistles), you can learn more about it [here](https://www.typescriptlang.org/docs/home.html).

To make sure you have everything set up correctly, type `tns preview`. Scan the barcode that appears with your Playground app. In a few seconds (your phone might switch between apps real quickly), you should see a screen resembling this:

<img class="alignnone wp-image-6" src="http://tomlisankie.com/blog/wp-content/uploads/2018/11/hello_world_nativescript-139x300.png" alt="" width="306" height="660" /> 

Let&#8217;s do a quick overview of the starter project that `tns` has generated for us. `hooks` houses code that runs before and after the app gets built and `node_modules` is the home of all of our Node dependencies for the app. The `app` directory is the real meat and potatoes of our project though: the UI elements and logic. Inside of that directory you&#8217;ll find JavaScript and TypeScript files for the view and view-model as well as XML files describing UI elements and their placements. There&#8217;s also a CSS file that defines the style the app will go by. `App_Resources` contain platform-specific files for both Android and iOS. Our `main-page.ts` file will contain all of our view logic and our `main-view-model.ts` file will contain all of our business logic. `app.ts` is the entry-point into our application.

## Creating a Model of a Conversation

Let&#8217;s build our model first. This will allow for us to build the &#8220;skeleton and muscle&#8221; first before we start layering &#8220;skin&#8221; (UI elements) on. To do this, open `main-view.model.ts` and delete everything below the `import` statement. We&#8217;ll start by creating a new class that will model the conversation between the user and the bot:

<pre><code class="language-typescript" lang="typescript">export class ConversationViewModel extends Observable {
    
}
</code></pre>

`Observable` is the NativeScript class that allows for easily creating new view-models which define the logic going on behind the view.

Now we can begin coming up with the logic that our app will use to model the conversation. What is a conversation? For our purposes, it&#8217;s a sequential exchange of information between two agents. It starts out with no content and then each message is &#8220;appended&#8221; to the conversation as time goes on. What data structure can we use to store sequences of content in order? An array! So let&#8217;s encode this into our ConversationViewModel:

<pre><code class="language-typescript" lang="typescript">userMessage = "";
chats = new ObservableArray([]);
listView = null;
</code></pre>

Since the conversation has not yet begun, we start off with a blank message from the user (we&#8217;ll worry about the bot later, its messages will be generated on our Heroku dyno). After this we declare not just a blank array to store the conversation history, but a blank `ObservableArray`. We&#8217;re using an `ObservableArray` because we need the conversation history to be able to detect changes that occur in itself which the ordinary `Array` class cannot do (remember to import it! `import { ObservableArray } from "tns-core-modules/data/observable-array/observable-array";`).

Next we need some way for the user to actually communicate a message to the bot. To do this we&#8217;ll create a function in our model called `onSend` for when the user finishes typing and taps the &#8220;send&#8221; button. We should also have a simple `if` statement to check if the input is valid and prepare the message before it leaves to go to the server. All together this leads us to a function that looks like this:

<pre><code class="language-typescript" lang="typescript">public onSend() {

    if(this.userMessage.trim() !== "") {

        this.chats.push({
            "who" : "user",
            "message" : this.userMessage
        });

    }

}
</code></pre>

So all that happens now when the function is called is it checks to make sure the message isn&#8217;t empty and appends a new JSON object to our chat history that contains who sent the message and the actual message itself. But the bot is never going to get to respond to the message because we never actually sent it. So how can we actually send the message off? For that, we&#8217;re gonna have to switch contexts for a bit and set up a Heroku instance for our app.

## Getting our Server Up and Running

Get out of your `ChatbotApp` directory and make a new directory called `ChatbotServer` and `cd` into it. This is where all of our backend code will live. We&#8217;re gonna use Node&#8217;s web app framework [Express](https://expressjs.com/). Type `npm init` to create a `package.json` file. You can do whatever you want for all of the fields, just make sure to type `server.js` as the entry point instead of the default `index.js`. After this process is done install Express in the directory by typing `npm install express`. Also make sure to install TypeScript in this directory by typing `npm install typescript` since we&#8217;re not in the `ChatbotApp` directory anymore. Install RiveScript as well (`npm install rivescript@^1.17.2`) since we&#8217;re gonna be using that for chatbot responses. Learning RiveScript is outside of the scope of this tutorial so if you need to learn more about it, you can do so [here](https://www.rivescript.com/). Also install the TypeScript specific files for Express and Process (Node module that allows sight to environment variables) by executing `npm install @types/express` and `npm install @types/node` Edit the `scripts` value in your `package.json` file to include a key-value pair that tells Node how to start. It should read:

<pre><code class="language-json" lang="json">"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node server.js"
  }
</code></pre>

Open up a new window in your text editor and create a new file called `server.ts` (**_not_** `server.js`). This is where all of our backend logic will be located. Let&#8217;s start writing our server by importing Express, RiveScript, and a parser for the messages between the user and bot, and creating a new class called `Server`:

<pre><code class="language-typescript" lang="typescript">import * as express from "express";
import rs = require("rivescript");
import * as bodyParser from "body-parser";

export class Server {
    
    
    
}
</code></pre>

Before we move any further, you should download [this](Add%20link%20here) and put the unzipped folder into your `ChatbotServer` directory. It contains a whole bunch of RiveScript files that we&#8217;ll use for sample conversations with our chatbot. Once you&#8217;ve done this, we can hop back in.

So how are we to communicate with this bot? Let&#8217;s consider a play scenario: our bot is a telemarketer. In order for our bot to be able to deal with customers efficiently, she needs to have scripts at hand that dictate what she can say when the customer says something and she needs to have a line to the outside world. Let&#8217;s go through each of the scenarios for the bot piece-by-piece:

<pre><code class="language-typescript" lang="typescript">public bot: rs;
public port: any;

constructor() {

    this.bot = new rs();
    this.bot.loadDirectory("brain", this.success, this.error_handler);

    this.port = process.env.PORT || 8080;

}
</code></pre>

First of all, the telemarketer (the bot) and her telephone line (the port) need to have space made for them. So we declare both variables. Next we actually bring the bot into existence, give her a location where it can find its scripts for the calls, and sets of directions on what to do if she does find scripts she can read and what to do when she can&#8217;t. Then we tell her which port to hook her telephone line into.

<pre><code class="language-typescript" lang="typescript">public success = () =&gt; {
    // the bot getting its papers in order so it can respond efficiently.
    this.bot.sortReplies();

    var app = express();
    app.use(bodyParser.json());
    app.set("json spaces", 4);
    app.post("/bot-reply-system", this.getReply);
    app.get("/", this.getUsage);
    app.get("*", this.getUsage);
    app.listen(this.port, function() {

        console.log("The server is running.");

    });

}

public error_handler = (loadcount, err) =&gt; {

    console.log("Error loading batch #" + loadcount + ": " + err + "\n");

}
</code></pre>

Each of these functions are lists of instructions on what our telemarketer should do if she is able to find all her scripts and read them properly. She should first sort them for efficient access (the customer expects a telemarketer to respond right away, not wait five seconds for her to shuffle through her scripts). After this she should set up her workstation that connects her telephone to the outside world. She turns on her signal processor (JSON parser in this context) and tells it what kind of signal it&#8217;s gonna be processing (JSON separated by 4 spaces here). Then she sets up different options for the user to choose from (think of this like those &#8220;press 1 for the front desk&#8230;&#8221; automated call routing services). She sets one up for actually talking to people who are interested in the product (`app.post("/bot-reply-system", this.getReply);`) and others for all other calls. Once all of this is set up, she can finally hook up her phone line and begin taking calls (`app.listen` here).

If she can&#8217;t find her scripts or read them properly, she tells us.

You may have also noticed that we&#8217;re using arrow functions for all of these instead of regular TypeScript functions. The reason for this is because it&#8217;s necessary in our setup since [JavaScript is weird with the concept of &#8220;this.&#8221;](https://basarat.gitbooks.io/typescript/content/docs/arrow-functions.html)

<pre><code class="language-typescript" lang="typescript">public getReply = (request, response) =&gt; {

    var username = request.body.username;
    var message = request.body.message;

    if(typeof(username) === "undefined" || typeof(message) === "undefined") {

        return this.error(response, "A username and message are both required.");

    }

    var reply = this.bot.reply(username, message);

    response.json({
        "status" : "ok",
        "reply" : reply
    });

}

public getUsage = (request, response) =&gt; {

    response.write("I'm not answering messages here, try POSTing to my reply system!");
    response.end();

}

public error(response, message) {
    response.json({
        "status": "error",
        "message": message
    });
}
</code></pre>

The first two functions are what are used for each of the different options the caller could choose from. If we&#8217;re being reached by a user from the proper channel, we have to generate a reply. The instructions for doing so are in the `getReply` function. We first get the caller ID (username) of the user and the message they&#8217;re trying to tell us. If the caller ID is coming up as &#8220;unknown&#8221; or the message is too garbled (if either of the two are &#8220;undefined&#8221;), the telemarketer should hang up the phone (use the `error` function in this snippet). Otherwise the telemarker generates a reply by referring to her scripts for the appropriate response and substituting the customer&#8217;s name in when necessary. The telemarketer then speaks the reply she generated back to the user (`response.json`).

If the telemarketer is being reached by the user from some other channel, we simply tell them we&#8217;re not taking calls on the line and close the call (respond to them and end the response).

Finally, add a declaration of a new Server instance (`var theServer = new Server();`) to the end of the file That&#8217;s all there is to how the server should operate! Now we need an actual building (container server) for the telemarketer to show up to work. For this, we&#8217;re gonna use [Heroku](https://heroku.com). If you don&#8217;t already have a Heroku account and the CLI installed for it, you should do that before you continue. Once you have the CLI set up, go back into your Terminal / Command Prompt and make sure you&#8217;re in the `ChatbotServer` directory. Execute these commands:

<pre><code class="language-shell" lang="shell">tsc server.ts
git init
git add .
git commit -m "First commit of the backend for the chatbot app."
heroku create
git push heroku master
heroku open
</code></pre>

Once done, you should see a page stating &#8220;I&#8217;m not answering messages here, try POSTing to my reply system!&#8221; in your browser. If you see that, your &#8220;telemarketer&#8221; is ready for work!

## Continuing Writing the App

Now that we have a backend to communicate with, we can build instructions to send messages to the bot. We&#8217;re going to use the Fetch module to communicate with the server, so import it at the top of your `main-view-model.ts` file in your `ChatbotApp` project. We&#8217;re also going to need to know _where_ we&#8217;ll be looking for the bot, so create a constant with your imports that contains the URL you went to when you typed `heroku open` earlier. Your imports section at the top of your file should now look something like this:

<pre><code class="language-typescript" lang="typescript">import { Observable } from "tns-core-modules/data/observable";
import { ObservableArray } from "tns-core-modules/data/observable-array/observable-array";
import {fetch} from "fetch";
const host = "https://tranquil-everglades-39497.herokuapp.com/"; //don't forget the backslash at the end of the URL!
</code></pre>

Once we have these, we&#8217;re ready to send the message off to the server. Navigate into your `onSend` method. To recap what we&#8217;ve done so far there, all we&#8217;re doing is checking if the user&#8217;s message has any text. If it does, we add it to a local list that&#8217;s keeping track of our conversation. Now we want to send that message off to the server for a response. To do this, we need to do an HTTP POST request to the `bot-reply-system` directory with our message information. This is what we were setting up for when building our server when we wrote `app.post("/bot-reply-system", this.getReply);`. To send a POST request with the message information, we can do this:

<pre><code class="language-typescript" lang="typescript">fetch.fetch(host + "/bot-reply-system", 
    {method: "POST", headers : {
        "Content-Type" : "application/json"
    },
    body : JSON.stringify({
        "username" : "user",
        "message" : this.userMessage
    })
})
</code></pre>

Here all that&#8217;s happening is we&#8217;re telling our app where to send the request to, telling it which kind of request method we&#8217;re using, telling it what kind of format to expect the content to be in, and then passing in a JSON object (formatted as a string) representing our message.

Once the server receives this message, it&#8217;s going to send a response. We have to write code to deal with this. Fortunately we can just chain together `then` methods to do this. Let me show you what I mean:

<pre><code class="language-typescript" lang="typescript">.then((response) =&gt; {

    if(!response.ok) {

        const responseObj = response.json();
        if(!response.error) {

            responseObj.error = "Something went wrong!";
            console.log(responseObj.error);

        }

        return responseObj;

    }

    return response.json();

})
</code></pre>

Here, we&#8217;ve received a response from the server and begin to inspect and unpackage it. We check to see if the server responded properly. If not, we print an error and return the response object and move on. If it was fine, we just return the response object as JSON and continue.

<pre><code class="language-typescript" lang="typescript">.then((response) =&gt; {

    const botReply = {
        "who" : "bot",
        "message" : ""
    };

    if(response.error) {

        console.log("Couldn't talk to the bot.");
        botReply.message = response.error;

    } else {

        botReply.message = response.reply;

    }

    this.chats.push(botReply);
    const count = this.listView.items.length;
    this.listView.scrollToIndex(count - 1);

});
</code></pre>

Here we&#8217;re updating our local resources to reflect the reply from the bot. We set up a new JSON object that&#8217;s the same as when we were initially making a new user message to add except the username is &#8220;bot.&#8221; We check to see if the response returned an error (this property was made in the previous `then` statement). If there was, we print this out in the console and make our bot&#8217;s &#8220;message&#8221; the response&#8217;s error. Otherwise, we set our local version of the bot reply&#8217;s message to the &#8220;reply&#8221; property from the response. Then we add this reply to our local chat history, count how many messages we have and then scroll our view of the chats down to the most recent message.

And that&#8217;s all there is to making a model of a conversation between our bot and a user! Let&#8217;s try running our app to see if it&#8217;s working. But before you do, you&#8217;re going to have to go into your `main-page.ts` file and change all occurrences of `HelloWorldModel` to `ConversationViewModel`. Otherwise the app will crash since HelloWorldModel no longer exists. Now, `cd` into your `ChatbotApp` directory and run `tns preview` and load the app to your device:

<img class="alignnone wp-image-7" src="http://tomlisankie.com/blog/wp-content/uploads/2018/11/app-still-same-139x300.png" alt="" width="293" height="632" /> 

It still looks the exact same! What gives? Well, we wrote all the logic for what should happen, but we never set up the interface so that the events _can_ occur. That&#8217;s the next step.

## Creating the Interface

We have everything set up except for the user interface. So how should we set that up? Well, first thing&#8217;s first, we should edit how it actually looks. The layout of the UI elements in our app (as mentioned previously) is described in XML files. `app-root.xml` just describes which page of the app to start at and the only other XML file in our project is `main-page.xml`. Inside of that file you&#8217;ll find a whole bunch of autogenerated comments as well as XML tags like `Label` and `Button` that describe each of the layout&#8217;s elements. I&#8217;m not gonna go in-depth on the properties of each of these tags so, if you&#8217;d like to learn more, you can read about them [here](https://docs.nativescript.org/ui/basics). You can go ahead and delete everything besides the `Page` tags, we&#8217;ll be writing our own. Your file should now look like this:

<pre><code class="language-xml" lang="xml">&lt;Page xmlns="http://schemas.nativescript.org/tns.xsd" navigatingTo="navigatingTo" class="page"&gt;

&lt;/Page&gt;
</code></pre>

All that we have now is a blank, default NativeScript page. It&#8217;s a blank canvas for us to put our components onto. Now what should we add here? I think an action bar would be nice. To add an action bar we can add a tag for it. Each Page has a property for action bars so we can do this:

<pre><code class="language-xml" lang="xml">&lt;Page.actionBar&gt;
    &lt;ActionBar title="Chatbot App" icon="" class="action-bar"&gt;
    &lt;/ActionBar&gt;
&lt;/Page.actionBar&gt;
</code></pre>

So adding elements is as simple as declaring them using the proper tags from top to bottom. Knowing this, we know that the chat interface itself must come next.

<pre><code class="language-xml" lang="xml">&lt;GridLayout columns="*" rows="*, auto"&gt;
    &lt;ListView height="90%" row="0" margin-bottom="50" padding="5" id="listView" items="{{ chats }}"&gt;
        &lt;ListView.itemTemplate&gt;
            &lt;StackLayout backgroundColor="white" id="chatBubble"&gt;
            &lt;/StackLayout&gt;
        &lt;/ListView.itemTemplate&gt;
    &lt;/ListView&gt;
&lt;/GridLayout&gt;

</code></pre>

What do we have here? All that&#8217;s here is we&#8217;re saying we&#8217;re gonna display the information as a list, set its sizing, give it an ID, and tell it where to look for the content. Inside of that, we define a template for what each item in the list view should be like. In this case we&#8217;re gonna stack items on top of one another and each item will be a chat bubble. A messaging interface is just a list view with seperate columns. With that in mind, let&#8217;s define what each of these columns will be like:

<pre><code class="language-xml" lang="xml">&lt;StackLayout  visibility="{{ who === 'bot' ? 'visible' : 'collapsed' }}"&gt;
    &lt;GridLayout width="100%" columns="*" rows="auto, 20" class="msg them"&gt;
        &lt;StackLayout orientation="horizontal"&gt;
            &lt;Label text="{{ message }}" textWrap="true" verticalAlignment="top" class="msg_text"/&gt;                                
        &lt;/StackLayout&gt; 

    &lt;/GridLayout&gt;
&lt;/StackLayout&gt;

&lt;StackLayout  visibility="{{ who === 'user' ? 'visible' : 'collapsed' }}"&gt;
    &lt;GridLayout columns="*, auto" rows="auto, 40" class="msg me"&gt;
        &lt;StackLayout col="1" orientation="horizontal" horizontalAlignment="right"&gt;
            &lt;Label text="{{ message }}" class="msg_text" textWrap="true" verticalAlignment="top" /&gt;
        &lt;/StackLayout&gt;
    &lt;/GridLayout&gt;
&lt;/StackLayout&gt;
</code></pre>

Here we declare two new stack layouts for each of the different participants in the conversation, each with a grid layout inside. The bot&#8217;s messages will appear in the left column and the user&#8217;s messages will appear in the right column. Messages from both users are posted to each column, it&#8217;s just that only messages for a particular user&#8217;s column are shown. Otherwise they&#8217;re collapsed. Now we need some interface components for actually typing and sending a message:

<pre><code class="language-xml" lang="xml">&lt;StackLayout row="1" id="chatbox"&gt;
    &lt;GridLayout columns="*,auto" backgroundColor="#006967" style="padding: 10"&gt;
        &lt;TextField 
            row="0" col="0"
            id="chatText"
            class="chatTextField"
            height="40"
            returnPress="{{ onSend }}"
            text="{{ userMessage }}"&gt;&lt;/TextField&gt;
        &lt;Button 
            row="0" col="1"
            id="chatBtn" 
            textTransform="none" 
            fontFamily="FontAwesome"
            height="40" padding="5" margin="5" 
            class="btn btn-primary" 
            text="send &#xf1d9;" tap="{{ onSend }}"&gt;&lt;/Button&gt;
    &lt;/GridLayout&gt;
&lt;/StackLayout&gt;
</code></pre>

We create a new text field for typing and a button for sending and link them to the appropriate properties and functions in the JS versions of our code.

## That&#8217;s all, folks!

And with that, we&#8217;re done! Congratulations, you just created your first cross-platform NativeScript chatbot application. Navigate back to your Terminal / Command Prompt and type `tns preview`. You should be able to give your new chatbot app a test drive (fyi: if you haven&#8217;t submitted any requests to your Heroku instance in 30 minutes or more, it&#8217;s gonna take a few extra seconds for it to respond while it wakes up. It should be instant after that). Again, if you missed anything or you&#8217;re not exactly sure where some parts of the code will go, you can find the full working code for the app [here](https://github.com/TomLisankie/ChatbotApp) and the full working server code [here](https://github.com/TomLisankie/ChatbotServer).

Be sure to follow me on Twitter! [@TomLisankie](https://twitter.com/TomLisankie)
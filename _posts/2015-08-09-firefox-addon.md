---
layout: post
title: "My First Firefox Add-on"
published: true
---

I have always wanted to create my own extension to the browser I use. Earlier, Chrome was my favorite. But, Firefox wins these days as Chrome is relatively slower. This time I really needed the add-on for myself.

###My Requirement

I'm on a network where it doesn't allow http sites most of the times. It prefers https for obvious reasons. I bookmarked my favorite sites. So, those don't cause trouble when I load those from the bookmarks. The problem I had was with the links that I get on Twitter or Facebook. Whenever I click on a link, it takes me to some t.co/** URL and my network frowns at me. I had to add https to the URL manually to proceed further.

So, it's clean and straight. I need to be able to convert the current URL from http to https. I decided to create a button and assign this functionality to it. Here starts my add-on.

![Developer Hub - Addon Fox](https://addons.cdn.mozilla.net/static/img/developers/hub-addon-fox.png)

###Mozilla Add-ons Site

First stop was the [mozilla add-ons site](https://developer.mozilla.org/en-US/Add-ons/SDK/Tools/jpm#Installation "Getting Started with Firefox add-ons"). jpm is the tool we need. Install it using npm.

```
npm install jpm -g
```

After installation, create a new directory for the add-on. Inside the directory, initialize a new add-on project.

```
mkdir myadd-on
cd myadd-on
jpm init
```

When you run `jpm init` from the command line, it prompts you a series of questions based on which your add-on's meta data is built in the `package.json` file. The developer site has a [detailed explanation](https://developer.mozilla.org/en-US/Add-ons/SDK/Tools/jpm#jpm_init) on what this step does. In short, it gives you the files and folders you need. Consider it like bootsraping your project. When you're done with the answers, a `package.json` is built for you. This contains the details you provided as answers for the prompt. jpm also asks you to confirm the details.

```
san@sanspace:~/workspace/js/temp $ jpm init
title: (My Jetpack Addon) My add-on
name: (temp) My Add-on
version: (0.0.1) 0.0.1
description: (A basic add-on) A basic add-on
entry point: (index.js) index.js
author: San
engines (comma separated): (firefox,fennec) firefox
license: (MIT) MIT
JPM undefined About to write to /home/ubuntu/workspace/js/temp/package.json:

{
  "title": "My add-on",
  "name": "myadd-on",
  "version": "0.0.1",
  "description": "A basic add-on",
  "main": "index.js",
  "author": "San",
  "engines": {
    "firefox": ">=38.0a1"
  },
  "license": "MIT"
}


Is this ok? (yes) yes
san@sanspace:~/workspace/js/temp $ 
```

Once you're done, you get the files and folders you need. Head to `index.js`. Now, it just has some sample code.

```javascript
var self = require('sdk/self');

// a dummy function, to show how tests work.
// to see how to test this function, look at test/test-index.js
function dummy(text, callback) {
  callback(text);
}

exports.dummy = dummy;
```

####The Development - Just a few lines of Javascript

I just had to add few lines, acutally a single line takes care of the functionality I need.

```javascript
var buttons = require('sdk/ui/button/action');

var button = buttons.ActionButton({
  id: "secureit-button",
  label: "Add HTTPS",
  icon: {
    "16": "./icon-16.png",
    "32": "./icon-32.png",
    "64": "./icon-64.png"
  },
  onClick: handleClick
});

function handleClick(state) {
  var tabs = require("sdk/tabs");
  // Change active tab url from http to https
  tabs.activeTab.url = tabs.activeTab.url.replace(/http:/, 'https:');
}
```

We import tabs and action button from the sdk. Then we create a button and assign an on-click event handler `handleClick` to it. Whenever this button is clicked, our function will be triggered. We can configure different icons to the button for different sizes.

The handler does a simple job. It grabs the URL of the current active tab. If it's a http URL, it changes it to https. The tab will be reloaded automatically to the new URL whenever we change it. That's all we need.

####Testing and Installation

You can test the add-on by running `jpm run`. It will run a firefox instance with the add-on. But, I was working on [cloud9](https://c9.io/ "Cloud9 IDE") editor. So, I skipped this step. I also skipped writing tests for the add-on as it's a very simple one. I won't recommend it though.

All that's left now is packaging the add-on.

```
jpm xpi
```

This will give you an xpi file which you can drag into firefox. It doesn't need a restart. So, we have the add-on we needed.

We can also submit our add-on to the mozilla site so other users can install it. Create an account at the [Developers Hub](https://addons.mozilla.org/en-US/developers/) and submit the XPI file there. It will be reviewed by the community and published.

My first add-on's here: <https://addons.mozilla.org/en-US/firefox/addon/add-https/>

Source is here: <https://github.com/sanspace/add-https/>

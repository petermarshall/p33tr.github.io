---
layout: post
title: Create a contact manager in a local db
categories: [electon, code, tutorial]
---
*Create a contact manager in a local db*

This is going to use electon, and sqlLite. It should work across windows and linux.

Create a nice fresh directory somewhere.
Navigate to that directory in a command window.

Open your browser and navigate to [patrickmoffitt/local-sqlite-example](https://github.com/patrickmoffitt/local-sqlite-example) because we are going to cheat a lot. Click the 'clone or download' button. copy that url (https://github.com/patrickmoffitt/local-sqlite-example.git)

Switch to your command window type in 'git clone ' and paste the url. Hit enter.
```
git clone https://github.com/patrickmoffitt/local-sqlite-example.git
```

That should download a whole lot of stuff.

There will be a directory called : local-sqllite-example
Navigate to that directory. In there will be a file called package.json



You need to install electron. Stay in the same root directory where the package.json file is.

```
npm install electron --save-dev
```

This will install electron. More stuff.

Start the application.
```
npm start
```

This will create a sqlLite database, and it will tell you where it is installed, with a line in the command window.

A user interface should appear. That allows you to enter names, edit names and delete names. That is a very very basic start.

See next post - How to make it more useful, by adding address, notes etc.

---
title: Creating Hitchy Project ... 
date: 2017-04-23 16:51:29
---

# ... From Scratch

If you want to develop a web application with Hitchy you could start creating a project from scratch. 

1. Using your favourite CLI create folder to contain your new project:  
   `mkdir myproject`
   
2. Of course, you'll need to enter it next:  
   `cd myproject`
   
3. Create new package.json file for managing your project and its dependencies:  
   `npm init`  
   Answer all questions as desired.
   
4. Install Hitchy as a dependency:  
   `npm i -S hitchy`
   
5. Mark your folder to actually contain some Hitchy application. Put `{}` in a file named **hitchy.json** using your favourite text editor. This file must be located next to **package.json** file created before.

6. Open your **package.json** file created before and add script in section named `scripts`. If that section is missing you might need to add it as well, of course:
   ```
   "scripts": {
     "server": "hitchy start"
   }
   ```
   
6. Start your Hitchy service:  
   `npm run server`


# ... As Part Of Some Existing Project

Well, Hitchy wouldn't be called hitchy, if it's not suitable for hitching onto something existing. Here comes one of two ways you should consider hitchy with regards to this framework.

> The second way is using Hitchy as a middleware injected into an existing project relying on ExpressJS. However, that scenario isn't covered in this tutorial, yet. You might want to check out tests included with Hitchy for more information.

Lets consider you want to start a web application using another super great framework: [VueJS](https://vuejs.org/). You might like to use one their templates to get started:

1. Using your favourite CLI install [vue-cli](https://www.npmjs.com/package/vue-cli) globally:  
   `npm i -g vue-cli`
   
2. Create new project from template using global script **vue** available by now:  
   `vue init webpack myproject`  
   Of course, you might choose any other template. Just answer all questions as desired. This will create a new subfolder named **myproject** in your current working directory.
   
3. Enter that folder now:  
   `cd myproject`
   
4. _Adjust this VueJS application to your needs. Just consider some time might have passed since you started the project before and you you think it's time to add Hitchy for backend stuff._

5. With your CLI (still) seeing your project's folder **myproject** as its current working directory create a new subfolder named **server**. Of course, you may name it as you prefer. But in steps below this folder is named **server**.

6. Install hitchy as another dependency of your basic project:  
   `npm i -S hitchy`
   
7. Put a file named **hitchy.json** into created subfolder **server** with content `{}`. This will properly mark that subfolder to contain a Hitchy-based (sub-)project.

8. Adjust the existing **package.json** file of your VueJS project adding another script in section `scripts` (which is existing due to VueJS already):  
   ```
   "scripts": {
     ...,
     "server": "hitchy start --project server --extensions ."
   }
   ```
   This extended invocation of Hitchy instructs its CLI to consider actual project residing in subfolder **server** while extensions to be added soon will be available in **node_modules** folder of containing VueJS application itself thus referring to the root directory of VueJS project.
   
9. Start your Hitchy service now:  
   `npm run server`


# See Hitchy In First Action

After starting hitchy you should read some output like this:

```
starting application using internal server ...

Service is running. Open 

   http://127.0.0.1:3000 

in your favourite browser now!



Hitchy is ready to serve requests, now.
```

Of course you might follow provided instruction and open URL http://127.0.0.1:3000 in your favourite web browser. This should result in a view like this one:

![](/images/hitchy-first-start-browser-error.png)

That view is actually provided by Hitchy's core which is solely up and running by now. This core is ready to handle additional routing to be set up soon. But for now it's answering every request with this screen indicating that the required view doesn't (yet) exist.

If you happen to be really fast or if there are some extensions to be bootstrapped later you might happen to see a different view presented by Hitchy's core:

![](/images/hitchy-splash.png)

Hitchy is pretty fast on opening socket listening for incoming connections. But it might need some time to set things up. This view is presented as some kind of "splash screen" indicating that Hitchy isn't ready to serve requests, yet.


# Ready for some actual interaction? 

See the [definition of custom routes](/getting-started/routes.html), next ...

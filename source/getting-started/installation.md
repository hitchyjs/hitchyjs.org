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
   `npm install hitchy`
   
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

1. Using your favourite CLI install [@vue/cli](https://www.npmjs.com/package/@vue/cli) globally:  
   `npm install -g @vue/cli`
   
2. Create new project from template using global script **vue** available by now:  
   `vue create myproject`  
   This will start a scaffolding process asking some questions. Answer them as desired. This will create a new subfolder named **myproject** in your current working directory.
   
3. Enter that folder now:  
   `cd myproject`
   
4. _Adjust this VueJS application to your needs. Just consider some time might have passed since you started the project before and you you think it's time to add Hitchy for backend stuff._

5. With your CLI (still) seeing your project's folder **myproject** as its current working directory create a new subfolder named **server**. Of course, you may name it as you prefer. But in steps below this folder is named **server**.

6. Install hitchy as another dependency of your basic project:  
   `npm install hitchy`
   
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

## Common Pitfall: CORS

### Development Setup

Developing a VueJS application usually involves running a development server of VueJS exposing your application for local testing supporting hot reloading. This is pretty useful but due to using different service your application won't be able to access the Hitchy server set up before due to CORS restrictions applied by your browser.

> When browsing your application via development server on http://localhost:8080 it can't access Hitchy server which is available via http://localhost:3000 due to browser considering both services providing different domains because of different port numbers. This is why CORS will prevent your application served via http://localhost:8080 from accessing service at http://localhost:3000 out of the box.

One option is to add proper CORS headers provided by Hitchy server. Another option is to use a plugin for Hitchy which is providing reverse proxy so you can access your VueJS development server via Hitchy server, thus using same service in same _domain_.

1. When in your project's root folder - which is the one created by VueJS CLI - add the [reverse proxy plugin](https://www.npmjs.com/package/hitchy-plugin-proxy) as a dependency:

   ```bash
   npm install -D hitchy-plugin-proxy
   ```
   
   This dependency will be discovered by Hitchy due to the way it is started with argument `--extensions .` as configured in step 8 above.
   
2. Add folder **server/config** unless created before. This folder is containing Hitchy's configuration in one or more files.

3. Add another configuration file for setting up reverse proxy plugin. The file's name doesn't matter but as a matter of convention you should name it just like the part of configuration it is exporting. In case of the proxy this is `proxy` and so you should name it **server/config/proxy.js**.

   Its content is
   
   ```javascript
   if ( process.env.NODE_ENV !== "production" ) {
     exports.proxy = [
       {
         prefix: "/",
         target: "http://localhost:8080/",
       },
     ];
   }
   ```

   > This configuration is providing proxy configuration unless running in a production environment. This is due to be used with _development_ server of VueJS, only. See the next chapter for how to integrate VueJS application in a production setup. 
   
   The actual configuration is setting up single reverse proxy instance forwarding all requests in scope of URL `/` (NOTE: that don't match any _more specific_ route configured in Hitchy) to the server at http://localhost:8080/ which is the development server of VueJS CLI service.
   
3. Restart the Hitchy server. After that your development setup is done. You still need to run the VueJS development server, but instead of using it directly you start your application via Hitchy server instead.

### Production Setup

When in production setup you probably want to use a similar setup with the VueJS application served via Hitchy server so it won't suffer from CORS limitations as well. In production, a VueJS application isn't served via special server but consists of a set of files to be served by any server you like. So this server should be Hitchy server for sure.

1. Install [another Hitchy plugin](https://www.npmjs.com/package/hitchy-plugin-static) available for exposing files in a certain subfolder of your Hitchy server.

   ```bash
   npm install hitchy-plugin-static
   ```

2. This plugin works pretty similar to the proxy plugin installed in chapter before. Create a configuration file **server/config/static.js** containing this:

   ```javascript
   if ( process.env.NODE_ENV === "production" ) {
     exports.static = [
       {
         prefix: "",
         folder: "build",
       },
     ];
   }
   ```
   
   This configuration is serving as a counterpart to the proxy's configuration in that it is applied in production mode, only. It is mapping any incoming request to files in folder **server/build** unless matching any more specific route of Hitchy server.
   
   Due to security limitations the exposed folder must be part of your Hitchy server's root folder which isn't your project's folder but the one containing the **hitchy.json** file, so it's subfolder **server**.
   
3. You'd probably want VueJS CLI service to put builds of your VueJS application right into that folder. Create file **vue.config.js** in your project's folder (which isn't **server**) unless this file has been created before. It is exporting your adjustment to VUE CLI service's configuration. The essential property here is `outputDir`.

   ```javascript
   module.exports = {
     outputDir: "server/build",
     baseUrl: "/",
   };
   ```
   
   The additional option `baseUrl` is set to make sure built files are addressing related files "relative" to document root of Hitchy's server.
   
4. Build your application whenever you like:

   ```bash
   npm run build
   ```

5. After restarting Hitchy - remember to use environment variable `IP=0.0.0.0` on starting Hitchy so its publicly addressable - you should be able to open your built Hitchy application which is capable of addressing the backend services exposed by Hitchy.

   ```bash
   NODE_ENV=production IP=0.0.0.0 npm run server
   ```

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

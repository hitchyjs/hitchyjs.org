# Getting Started: Standalone Server

This tutorial is a simple step-by-step manual showing you how to start a new Hitchy-based project which isn't injected into some existing project but starting its own.

## Prerequisites

Hitchy is a framework written in Javascript. It's suitable for creating server-side software and thus doesn't run in a browser but relies on [Node.js](https://nodejs.org/en/). It's installation is a mandatory prerequisite.

You might want to install database backends so Hitchy will be able to use them for storing data but that's not required. Storing data with extension hitchy-odem there is support for using regular files for storing data. This backend isn't suitable for most production setups, but it is definitely sufficient for developing an application.

While developing your application we advise you to use [git](https://git-scm.com/) for managing versions of your source code locally (and remotely). This isn't a mandatory prerequisite either, but some advise not even related to Hitchy-based application development in particular. 

IDEs such as the freely available [VisualStudio Code](https://code.visualstudio.com/) or the commercially available [JetBrains WebStorm](https://www.jetbrains.com/webstorm) come with basic git supported included so yo might prefer using it.

## Creating The Project

Hitchy currently doesn't come with scaffolding tools and thus you'll have to start your application manually by creating a folder to contain your application first.

```bash
mkdir my-project
```

This CLI command is available in Linux, Unix and macOS creating folder **my-project** in current working directory. You might use similar tools when working with Windows.

Next switch to the project folder and create a configuration for the package manager of Node.js called `npm`:

```bash
cd my-project
npm init
```

The latter command is available due to having installed Node.js before. It is asking some questions about your project and eventually creates a file called **package.json** which is essential to your application.

### _Optional:_ Starting Git Repository

::: tip
Git is a pretty powerful tool. You should made yourself familiar with it e.g. by reading its [manual](https://git-scm.com/doc) or [some interesting introduction](https://www.atlassian.com/git/tutorials/what-is-version-control). This chapter is a very shallow and very simple introduction sufficient to start a repository in your project's folder.
:::

Create a new git repository in your project's folder. A _git repository_ is containing all versions of files in your project you want to keep track of.

```bash
git init
```

This command will create a new usually hidden folder named **.git** containing data you basically shouldn't care about in detail. Just keep the folder and don't delete it while managing versions of your files locally, only. After having finished this chapter you could remove any file or folder in your project except for **.git** and its content without ever loosing the history of your files.

Next create a file named **.gitignore** tracking names of files and folders that shouldn't be covered by git-based version control. Start with the following content:

```
.vscode
.idea
node_modules
npm-debug.log
```

This file is preventing your IDE's configuration as well as its installed dependencies from being tracked in your version control system unnecessarily.

Now add _all_ currently existing _essential_ files to your git repository using this command:

```bash
git add .
```

This will instruct git to track the files for changes. You need to add files to git this way whenever creating new files to be tracked.

The following command should be invoked every time you want to take a snapshot of your project's files and folders so you could restore it afterwards:
 
```bash
git commit -a
```

The command will open text editor asking for a description of changes that lead to the snapshot. The command `git log` will use this description to list snapshots in your repository.

## Installing Dependencies

Invoke the following command to add Hitchy as a dependency:

```bash
npm install --save hitchy
```

::: tip
Hitchy doesn't require any extension to work even though it comes with little instantly useful functionality either. The rest of this chapter is providing one of several commonly useful approaches to get quickly up and running with server-side business logics for a web-based application.
:::

Using the same feature of npm you can add extensions to Hitchy or any other tool you are going to use in your application in addition:

```bash
npm install --save hitchy-plugin-odem-rest
```

This example will install [an extension for Hitchy](https://www.npmjs.com/package/hitchy-plugin-odem-rest) to be available in your project. The extension is exposing structured data over REST API so you can very easily access and control declared data using HTTP requests. The management of that structured data is realized by another extension to Hitchy named [hitchy-odem](https://www.npmjs.com/package/hitchy-odem) which is a dependency of the one you've installed here.

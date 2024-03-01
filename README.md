# How to deploy using Render

**Table of Contents:**
- [Overview](#overview)
  - [Render vs. Github Pages](#render-vs-github-pages)
  - [Create An Account](#create-an-account)
- [A simple backend app](#a-simple-backend-app)
- [A more complex fullstack app](#a-more-complex-fullstack-app)
  - [Before you begin…](#before-you-begin)
  - [Okay… now you can deploy:](#okay-now-you-can-deploy)
  - [Future changes to your code](#future-changes-to-your-code)


## Overview

### Render vs. Github Pages
* Github Pages provides **static site hosting**. 
  * This means that the server that Github Pages runs on your behalf can only send static files to the client (HTML, CSS, and JS files). 
  * Github Pages static sites are not capable of receiving or sending messages via HTTP. 

* Render provides **web service and database hosting** (it can also host static sites). 
  * This means that the server that Render runs on your behalf can send static assets, receive and send messages via HTTP, and interact with a database. 
  * Render also can host your database giving you a one-stop-shop for running your fullstack application.

### Create An Account

Start by creating an account using your **GitHub** account. This will let you easily deploy straight from a GitHub repository.

![create an account using GitHub](./images/create-account.png)

This will take you to your Dashboard where you can see existing deployments.

![alt text](./images/dashboard.png)

## A simple backend app

1. Make sure you are signed in using your GitHub account
2. https://dashboard.render.com/ and click on <kbd>New +</kbd>
3. Select **Web Service**
4. Build and deploy from a Git repository
1. Find and Connect your GitHub repository
2. Find your repository and select **Connect**
3. Fill out the information for your Server
  * Name - the name of your app (it will appear in the URL that render gives you. For example: app-name-here.onrender.com)
  * Region - select US East (Ohio)
  * Branch - main
  * Root Directory - `server/` or wherever your `index.js` file lives. Use `./` if it is in the root of your repo.
  * Runtime - Node
  * Build Command - leave blank
  * Start Command - `node index.js`
  * Instance Type - select **Free**
* Select **Create Web Service**

 This should take you to your web service's dashboard where you can see the latest build information and the URL. In a few minutes your server will be up and running!

 Any time that you want to make an update to your deployed server, just commit and push the change to your repo!

![alt text](./images/web-service-dashboard.png)

## A more complex fullstack app

### Before you begin…
Before deploying your application, update the following files in the `main` branch of your repo:

* `src/index.js`

  * [Here is an example of what your index.js file should look like](https://github.com/The-Marcy-Lab-School/react-express-auth/blob/main/src/index.js) (you can just copy/paste the contents of this file)
  
* `knexfile.js `
  * [Here is an example of what your knexfile.js file should look like](https://github.com/The-Marcy-Lab-School/react-express-auth/blob/main/knexfile.js) (feel free to copy/paste the contents of this file) 
  * **Changes**: Add a `production` field in the `module.exports` object (copy from `development`). Then, set the `connection` value to `process.env.PG_CONNECTION_STRING`  (you'll set this environment variable in step 4)
  * **Why?** When you deploy your application, the `NODE_ENV` will be set to `production`. Your `knex.js` file uses this environment variable to select the appropriate configuration (the fallback is `'development'`). The `PG_CONNECTION_STRING` serves the same function as the object containing `host`, `port`, etc…. You'll see what this value looks like in step 4.
* Push the changes to your remote repository.

### Okay… now you can deploy:
1. Make sure you have an account on https://render.com/ and that you sign in using Github

2. Create a Postgres Server
   * https://dashboard.render.com/ and click on <kbd>New +</kbd>
   * Select **PostgreSQL**
   * Fill out information for your DB (leave all other fields blank)
     * Name - the name of your application
     * Region - select US East (Ohio)
     * Instance Type - select Free
   * Select **Create Database**
   * Keep the created database page open. You will need the `Internal Database URL` value from this page for step 4. This URL will look follow this pattern:

```
postgresql://user:password@host/dbname
```


3. Deploy Your Express Server
* https://dashboard.render.com/ and click on <kbd>New +</kbd>
* Select **Web Service**
* Connect your GitHub account (if not connected already)
* Find your repository and select **Connect**
* Fill out the information for your Server
  * Name - the name of your app (it will appear in the URL that render gives you. For example: app-name-here.onrender.com)
  * Region - select US East (Ohio) - the important thing is that it matches the PostgreSQL region
  * Branch - main
  * Root Directory - if you used the React Express Auth Template, leave this blank
  * Runtime - Node
  * Build Command - if you used the React Express Auth Template, use npm i && npm run migrate:rollback && npm run migrate && npm run seed
  * Start Command - if you used the React Express Auth Template, use npm start
  * Instance Type - select Free
* Select **Create Web Service** (Note: The first build will fail because you need to set up environment variables)


4. Set up environment variables
  * From the Web Service you just created, select Environment on the left side-menu
  * Under Secret Files, select Add Secret File
    * Filename - .env
    * Contents
      * Look at your local .env file and copy over the `SESSION_SECRET` variable and value.
      * Add a second variable called `PG_CONNECTION_STRING`. Its value should be the `Internal Database URL` value from step 2e above. 
      * Add a third variable called `NODE_ENV`. Its value should be `'production'`
      * Your file  should look like this:
  
  ```
  SESSION_SECRET='AS12FD42FKJ42FIE3WOIWEUR1283'
  PG_CONNECTION_STRING='postgresql://user:password@host/dbname'
  NODE_ENV='production'
  ```

  * Click Save Changes

### Future changes to your code
If you followed these steps, your Render server will redeploy whenever the `main` branch is committed to. To update the deployed application, simply commit to `main`. 

For frontend changes, make sure to run `npm run build` to update the `public/` folder contents and push.
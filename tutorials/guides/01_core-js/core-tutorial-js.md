## Introduction

This tutorial is designed to walk you through every aspect of **Glue42 Core**:

- project setup with the [**Glue42 Core CLI**](../../glue42-core/what-is-glue42-core/core-concepts/cli/index.html); 
- initializing [**Glue42 Client**](../../glue42-core/what-is-glue42-core/core-concepts/glue42-client/index.html) applications;
- extending the applications in the **Glue42 Core** project with [Interop](../../core/capabilities/interop/index.html), [Window Management](../../core/capabilities/window-management/index.html) and [Shared Contexts](../../core/capabilities/shared-contexts/index.html) functionalities;

The tutorial uses plain JavaScript and its goal is to show you how to use the **Glue42 Core** platform in your web projects.

There are also [React](../react/index.html) and [Angular](../angular/index.html) tutorials, but we recommended that you complete the JavaScript tutorial first in order to get to know **Glue42 Core** concepts and capabilities without the distractions of additional libraries and frameworks.

For the purpose of this tutorial, you are a part of the IT department of a big multi-national bank and you have been tasked to create an application that will be used by the bank Asset Management department. This will be a multi-app project consisting of two applications:
- **Clients** - displays a full list of clients and their details;
- **Stocks** - displays a full list of stocks with prices. When the user clicks a stock, its details should be displayed;

The two applications must be hosted on the same domain where:
- `/clients` resolves to the Clients app;
- `/stocks` resolves to the Stocks app;

As an end result, your users want to be able to run the two apps as Progressive Web Applications in separate windows in order to take advantage of their multi-monitor setups. Also, our users want these apps, even though in separate windows, to be able to communicate with each other. For example, when a client is selected, the stocks app should show only the selected client's stocks and so on.

## Prerequisites

This tutorial assumes that you have fundamental knowledge of JavaScript and asynchronous programming.

It is also recommended that you use the [**Glue42 CLI**](../../core/core-concepts/cli/index.html), [Glue42 Environment](../../core/core-concepts/environment/overview/index.html), [**Glue42 Client**](../../core/core-concepts/glue42-client/overview/index.html) and [**Glue42 Web**](../../reference/core/latest/glue42%20web/index.html) API documentation as reference if necessary.

## Tutorial Structure

The tutorial code is located in the **Glue42 Core** [**GitHub repo**](https://github.com/Glue42/core). The repo contains a `/tutorials` directory with the following structure:

```cmd
/tutorials
    /angular
        /solution
        /start
    /guides
        /01_javascript
        /02_react
        /03_angular
    /rest-server
    /javascript
        /solution
        /start
    /react
        /solution
        /start
```

- `/guides` - holds the tutorial text files;
- `/angular`,`/javascript` and `/react` - hold the starting and solution files for each respective tutorial;
- `/rest-server` - a simple server used in the tutorials to serve the data that you will need for the applications in JSON format;

**Glue42 Core** is an open-source project, and as such, all feedback and contributions regarding our code base and the tutorials are welcome.

## 1. Setup

First, you need to get the tutorial code and familiarize yourself with it. Clone the **Glue42 Core** [GitHub repo](https://github.com/Glue42/core).

### 1.1. Data REST Server

Navigate to the `/tutorials/rest-server` directory. This server is going to serve the data for our applications. Open a command prompt in the `/rest-server` directory and run the following commands:

```cmd
npm i

npm start
```

This will launch the server at port 8080.

### 1.2. Starting Files

Next, go to the `/tutorials/javascript/start`. These are the start files for your project. You can copy or move the files to work somewhere else, but for the examples in this tutorial it is assumed that you are working in the `/start` directory.

There you can find the following resources:

- `/assets` - holds shared assets for both applications like icons and `.css` files;
- `/lib` - holds common libraries used by both applications - the built [**Glue42 Web**](../../reference/core/latest/glue42%20web/index.html) script;
- `/clients` - the Clients app, which consists of only an `index.html`, a script file and a `manifest.json`;
- `/stocks` - the Stocks app which has the same elements as the Clients app, with the addition of the `/stocks/details` directory, which holds the `.html` and `.js` files for the stock details view;
- `favicon.ico` - a standard favicon;
- `package.json` - a standard `package.json` file describing the project;
- `service-worker.js` - used by both applications in conjunction with their manifests in order for the apps to be classified as installable [**Progressive Web Apps**](https://developer.mozilla.org/nl/docs/Web/Progressive_web_apps), but does not contain any meaningful logic;

### 1.3. The Glue42 CLI

Now, you are going to use the [**Glue42 CLI**](../../glue42-core/what-is-glue42-core/core-concepts/cli/index.html) to automatically setup all necessary [Glue42 Environment](../../core/core-concepts/environment/overview/index.html) files. Install the Glue42 CLI globally and then run its `init` command in the `/tutorials/javascript/start` directory:

```cmd
npm install --global @glue42/cli-core

gluec init
```
Or:

```cmd
npm install --save-dev @glue42/cli-core

npx gluec init
```

This will install the necessary dependencies and scaffold the necessary configuration files for you. 

Next, you have to configure the development server that the Glue42 CLI provides. The development server allows you to serve or proxy to your apps, define shared resources and serve the [Glue42 Environment](../../core/core-concepts/environment/overview/index.html) files correctly.

Open the `glue.config.dev.json` file that was created in the `/start` directory with the `init` command of the Glue42 CLI. Define the shared resources and the apps in the project. For detailed information on how to configure the `glue.config.dev.json` file, see the [**Glue42 CLI**](../../core/core-concepts/cli/index.html#configuration) documentation, or simply copy the configuration of the `apps` and `sharedAssets` properties below if you are using the default tutorial structure and files:

```json
{
    "glueAssets": ...,
    "server": {
        "settings": ...,
        "apps": [
            {
                "route": "/",
                "file": {
                    "path": "./"
                }
            },
            {
                "route": "/clients",
                "file": {
                    "path": "./clients/"
                }
            },
            {
                "route": "/stocks",
                "file": {
                    "path": "./stocks/"
                }
            }
        ],
        "sharedAssets": [
            {
                "route": "/assets",
                "path": "./assets/"
            },
            {
                "route": "/lib",
                "path": "./lib/"
            },
            {
                "route": "/favicon.ico",
                "path": "./favicon.ico"
            },
            {
                "route": "/service-worker.js",
                "path": "./service-worker.js"
            }
        ]
    },
    "logging": "default"
}
```

To launch the development server, run the `serve` command in the `start` directory:

```cmd
gluec serve
```

All files and apps defined in the `glue.config.dev.json` file are now served at `localhost:4242`. 

Open the browser at `localhost:4242/clients` and explore the Clients app. It is just a list of clients fetched from the REST server you started at the beginning. Clicking on a client will not do anything. If you look at the right side of the browser address bar, however, you will see an icon with the option to install the app. Once installed, you can launch it by going to `chrome://apps` (if you are use Google Chrome) and clicking on its icon.

Repeat this with `localhost:4000/stocks` to explore the Stocks app. It is just a simple list of stocks. The only difference here is that if you click on a stock, you will be redirected to the Stock Details view of the app. Once again you can install the app as a PWA.

You now have a running multi-application **Glue42 Core** project. Next, you will transform the two apps into [**Glue42 Clients**](../../core/core-concepts/glue42-client/overview/index.html) by initializing the [**Glue42 Web**](../../reference/core/latest/glue42%20web/index.html) library in each of them.

### 1.4. Solution Files

Before you continue with the project, take a look at the `/solution` directory. It contains the complete solution of the **Glue42 Core** JavaScript tutorial. You can use it after each section to compare it to your solution of the problem, or you can use it as a help reference in case you get stuck on something.

To run the solution project, launch the REST server providing the app data (see [1.1. Data REST Server](#1_setup-11_data_rest_server) above), open a command prompt in the `/javascript/solution` directory, install the dependencies and launch the solution project with the Glue42 CLI:

```cmd
npm i

gluec serve
```

You can now access the Clients app at `localhost:4242/clients` and the stocks app at `localhost:4242/stocks`.

## 2. Initializing Glue42 Web

Go over to each one of the `Clients/index.html`, `Stocks/index.html`, and `Stocks/details/index.html` pages, and include a new `<script>` after the **<!--TODO: Chapter 2-->** and before `<script src="./index.js">`, if it is present, which references the Glue42 Web script in the lib directory:

```html
<script src="/lib/web.umd.min.js"></script>
```

Next go to each one of the scripts at:
- `/Clients/index.js`
- `/Stocks/index.js`
- `/Stocks/details/index.js`

Look for the **TODO: Chapter 2** comment inside the `start` functions
in `/Clients/index.js`, `/Stocks/index.js`, and `/Stocks/details/index.js`. You should initialize [**Glue42 Web**](../../reference/core/latest/glue42%20web/index.html). Assign the `glue` object to the global `window` object for easy use.

```javascript
// in start()
window.glue = await window.GlueWeb();
```
Uncomment the `toggleGlueAvailable` function from `/Clients/index.js` and `/Stocks/index.js` and call it once the `window.GlueWeb` factory function resolves.

```javascript
toggleGlueAvailable();
```

After refreshing the apps, you should see in the top left corners of the **stocks**, **clients**, and **details** apps text that indicates that Glue is available. This means that you are successfully connected to the [**Glue42 Core Environment**](../../glue42-core/what-is-glue42-core/core-concepts/environment/index.html).

Good job, we now have everything we need to start adding more functionality.

## 3. Interop

### 3.1. Overview

In this section we will take a look at some functionality provided by our [**Interop API**](../../reference/core/latest/interop/index.html).

### 3.2. Methods and Streams registration

Whenever a user clicks on a client, we would like the **stocks** app to show only stocks owned by this client. We will achieve this by registering an interop method inside the `stocks` app, which whenever invoked will receive the selected client's portfolio and re-render the stocks table. We also want the **stocks** app to create a stream into which we will push the new stock prices and thereby the stream's subscribers will get notified, whenever new prices are generated.

Head over to `/Stocks/index.js`.  Right below where you invoked `toggleGlueAvailable`, we will register a method called `SelectClient`. This method will expect as an argument an object with property `client` that has a `portfolio` property. Next we need to filter all the stocks and pass only the correct ones to the function `setupStocks`, which will re-build our stocks table. After that we will create a stream called `LivePrices`, which once created we will assign to the global `window` object for easy access:

```javascript
// place code in start()
window.glue.interop.register('SelectClient', (args) => {
    const clientPortfolio = args.client.portfolio;
    const stockToShow = stocks.filter((stock) => clientPortfolio.includes(stock.RIC));
    setupStocks(stockToShow);
});

window.priceStream = await glue.interop.createStream('LivePrices');
```
Finally we will go over to the `newPricesHandler` function which is invoked every time new prices are generated and push the `priceUpdate` object to the stream. We will use it in the Window Context section. When you are done, your code should look something like this:

```javascript
// updated newPricesHandler
const newPricesHandler = (priceUpdate) => {
    priceUpdate.stocks.forEach((stock) => {
        const row = document.querySelectorAll(`[data-ric='${stock.RIC}']`)[0];

        if (!row) {
            return;
        }

        const bidElement = row.children[2];
        bidElement.innerText = stock.Bid;

        const askElement = row.children[3];
        askElement.innerText = stock.Ask;
    });
    if (window.priceStream) {
        window.priceStream.push(priceUpdate);
    }
};
```

Right now we can't see anything different in the browser, but have done a lot. Next we will find and use the method from within the `clients` app.

### 3.2. Methods discovery

Now, let's go over to `/clients/index.js` and extend our client click functionality. Locate the `clientClickedHandler` function, which is invoked every time a client is clicked and extend its logic to find if there is a registered method by the name `SelectClient`. We will use `glue.interop.methods()` to check if we have a match. Your code should look something like this:

```javascript
// clientClickedHandler
const selectClientStocks = window.glue.interop.methods().find((method) => method.name === 'SelectClient');
```

### 3.3. Methods invocation

Let's finish this client selection functionality by invoking the method we found, if it is present. Remember that the **Clients** and **Stocks** apps are designed to be launched and used on their own, so if there is no stocks app open, there will be no method to invoke.

```javascript
const clientClickedHandler = (client) => {
  const selectClientStocks = window.glue.interop.methods().find((method) => method.name === 'SelectClient');

  if (selectClientStocks) {
      window.glue.interop.invoke(selectClientStocks, { client });
  }
};
```

Awesome work! After refreshing the two apps should be interoping ("talking" with one another). Let's see how we can extend the client details functionality.

## 4. Window Management

### 4.1. Overview

Currently whenever you click on a stock, you are redirected to a new view. This works fine, but our users use multiple monitors and want to take advantage of that. To do that, they wish that whenever a stock is clicked we do **not** redirect, but open a new window with the stock details instead. They also want to have the new window opened with specific dimensions and position. To do all of that we will utilize the [**Window Management API**](../../reference/core/latest/windows/index.html).

### 4.2. Opening windows at runtime

Head over to the **Stocks** app and locate the `stockClickedHandler` function. It handles the stock click logic, which currently just rewrites the `window.location.href`. Let's remove that and use `glue.windows.open` to open a new window with the same URL. Your code should look like this:

```javascript
const stockClickedHandler = (stock) => {
    sessionStorage.setItem('stock', JSON.stringify(stock));
    window.glue.windows.open(`${stock.BPOD} Details`, 'http://localhost:4242/stocks/details/').catch(console.error);
};
```

After refreshing, whenever we click on a stock, we don't get redirected and instead a separate stock details window is opened. We currently use the `sessionStorage` to pass the data to the details window. In section 4.4. we will instead take advantage of the ability to pass a [**context**](https://docs.glue42.com/reference/core/latest/windows/index.html#!CreateOptions-context) object to a window.

### 4.3. Window Settings

Let's first extend our `windows.open` logic so that the new window has exactly 550 pixels width and height and its top/left coordinates are 100/100. We can easily do that by passing a config object to `windows.open`. Here is how that looks like:

```javascript
const stockClickedHandler = (stock) => {
    const openConfig = {
        left: 100,
        top: 100,
        width: 550,
        height: 550
    };
    sessionStorage.setItem('stock', JSON.stringify(stock));
    window.glue.windows.open(`${stock.BPOD} Details`, 'http://localhost:4242/stocks/details/', openConfig).catch(console.error);
};
```

### 4.4. Window Context

Let's wrap this requirement up by passing our selected stock to the stock details window. Once again, this is very easy and all we need to do is pass a context object to `window.open`. This context object is just a standard JS object. The final version of our `stockClickedHandler` should look like this:

```javascript
const stockClickedHandler = (stock) => {
    const openConfig = {
        left: 100,
        top: 100,
        width: 400,
        height: 400,
        context: stock
    };

    window.glue.windows.open(`${stock.BPOD} Details`, 'http://localhost:4242/stocks/details/', openConfig).catch(console.error);
};
```

Next, we need to update our `/stocks/details` to correctly get the stock object. To do that we need to get the context of our window and pass it to the `setFields` function, which renders the page:

```javascript
const start = async () => {
    window.glue = await window.GlueWeb();

    const stock = window.glue.windows.my().context;

    setFields(stock);
};
```

Now, that looks much better. Let's wrap this section up, by subscribing to the price stream we created previously, so that both our `/stocks` and `/stocks/details` can display live price data. Right below `setFields` in `/stocks/details` use the [**Interop API**](../../reference/core/latest/interop/index.html) to subscribe to the `LivePrices` stream. When the subscription resolves, set the `onData` callback to receive the stream data, find the correct price of our selected stock and pass the **Ask** and **Bid** prices to the `updateStockPrices` function. The end result, should look like this:

```javascript
// in start()

const subscription = await window.glue.interop.subscribe('LivePrices');
subscription.onData((streamData) => {
    const newPrices = streamData.data.stocks;
    const selectedStockPrice = newPrices.find((prices) => prices.RIC === stock.RIC);
    updateStockPrices(selectedStockPrice.Bid, selectedStockPrice.Ask);
});
```

If you have made it this far without checking out the solution, then awesome work! Let's continue onto the last section where we will link the `clients`, `stocks` and `stock details` windows together.

## 5. Shared Contexts

Our clients love the project so far. They are going wild taking full advantage of their multiple monitors thanks to our work with the [**Interop API**](../../reference/core/latest/interop/index.html) and the [**Window Management API**](../../reference/core/latest/windows/index.html). The users' final request is to be able to see in the `stock details` window if the selected client has the stock in their portfolio. So far `clients` and `stock details` had no interaction between each other, so let's change that.

We could do the same like we did in `stocks` and register a method, but we have a feeling that once the clients start using our app, they will require more integrations with more apps. So, let's complete their request by also allowing ourselves to easily hook more apps to this logic in the future, using the [**Shared Contexts API**](../../reference/core/latest/shared%20contexts/index.html).

We begin by going back to the `clients` script file and inside the `clientClickedHandler` function. We will comment out the current code and will use the [**Shared Contexts API**](../../reference/core/latest/shared%20contexts/index.html) to update the shared context with the selected client. This will allow other applications to subscribe to changes to the shared context and get notified on client selection.

```javascript
const clientClickedHandler = (client) => {
    // const selectClientStocks = window.glue.interop.methods().find((method) => method.name === 'SelectClient');

    // if (selectClientStocks) {
    //     window.glue.interop.invoke(selectClientStocks, { client });
    // }

    window.glue.contexts.update('SelectedClient', client).catch(console.error);
};
```

Now, let's head over to `stocks` script file and edit the `start` function. We want to subscribe to changes to the shared context with key `SelectedClient`, once there is a change, our callback will be invoked and a client object will be passed.

```javascript
// start func

window.glue.contexts.subscribe('SelectedClient', (client) => {
        const clientPortfolio = client.portfolio;
        const stockToShow = stocks.filter((stock) => clientPortfolio.includes(stock.RIC));
        setupStocks(stockToShow);
});

window.priceStream = await glue.interop.createStream('LivePrices');
```

There are a couple of things to mention here. As you will quickly see, we don't have any logic to remove a selected client from the shared context. This is something that we should definitely do before deploying to production and you have all the skills now to do it. You could add a button somewhere to deselect the client or anything else you desire. Also, if you notice, the `stocks` app listens for a selected client on both the method registered in the beginning and the shared contexts. This could be a desired functionality or maybe an overkill, again it is up to you.

One last thing we need to do, before we ship a beta version to our users is to make sure the `stock details` also subscribes to the shared context and displays whether or not the selected client has the displayed stock. Once we get the selected client from the shared context, we pass it to the `updateClientStatus` together with the stock object we have from before. Also, go over to `/stocks/details/index.html` and uncomment the section marked with **TODO: Chapter 5**.

```javascript
window.glue.contexts.subscribe('SelectedClient', (client) => {
    updateClientStatus(client, stock);
});
```

## Congratulations

You have completed the Glue42 Core Vanilla JS tutorial, awesome work! If you are a React developer, we recommend checking out our React tutorial. In the meantime go out there and make some awesome apps!

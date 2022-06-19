# create-svelte

Everything you need to build a Svelte project, powered by [`create-svelte`](https://github.com/sveltejs/kit/tree/master/packages/create-svelte).

## Creating a project

If you're seeing this, you've probably already done this step. Congrats!

```bash
# create a new project in the current directory
npm init svelte

# create a new project in my-app
npm init svelte my-app
```

## Developing

Once you've created a project and installed dependencies with `npm install` (or `pnpm install` or `yarn`), start a development server:

```bash
npm run dev

# or start the server and open the app in a new browser tab
npm run dev -- --open
```

## Building

To create a production version of your app:

```bash
npm run build
```

You can preview the production build with `npm run preview`.

A Stock Watchlist App
The goal of this app is to provide a way for a user to manage watchlists of quotes.

Framework Requirements
You must use https://svelte.dev/ to develop the front end.
You may use a server side framework if you like, it's not required
If you choose to use a database, it must be an embedded one like Sqlite
Local browser storage is acceptable
You should use a modern CSS layout framework such as flexbox or css-grid.
You cannot use any jQuery
Github
Your code submission must be committed into a single github project. It should be a private one.
Your github project must include a “README.md” file that has clear instructions on how to setup and run your project.
You will give @hcnguyen5, @amamparo, and @dgore7 read access to your repository when you're ready for review.
Quote Data Source
You should use IEX Cloud’s API to get the current stock quotes: https://www.iexcloud.io/docs/api/#quote

 

You should be able to use a free trial plan.

Symbol Search Endpoint
https://vast.tastyworks.com/symbols/search/<Search String>

 

Example:

https://vast.tastyworks.com/symbols/search/GOO

Application Requirements
When the app first starts up, the user should be prompted to enter their username.
A password is not required. User registration and proper authentication should not be done for this exercise.
After the user is established, the app should transition to a Watchlist screen.
If this is a new user, a default watchlist should be created for the user
The default watchlist’s name should be “{user’s first name} first list”
The default watchlist should have the following symbols: AAPL, MSFT, SPY
If this is a returning user, the user's previously created watchlists should be loaded
The Watchlist screen should consisting of the following elements:
A way to switch watchlists
The watchlist table composed of the following columns:
Stock Symbol
Description
Bid Price
Ask Price
Last Price
A way to modify a watchlist
Add a symbol to an existing watchlist
Remove a symbol from an existing watchlist
Create a new watchlist with a given name
Delete a watchlist
When the user is adding a symbol to a watchlist, they should be presented with a symbol search screen to allow for auto-completion of a symbol.
Symbol auto-completion should done using the symbol search endpoint specified above
The application should refresh the quotes on the active watchlist every 5 seconds.
When a user selects a symbol from a watch list it should bring up a new view that displays the Stock Symbol, Bid, Ask, and Last Price As well as a chart.
The chart should show the last 30 days of the symbol’s last price history.
The stock price history chart should render a point for the average price per historic quote
You can retrieve the last 30 days of data from IEX’s chart api.
Feel free to use an open source library to interact with IEX.
The quote prices should update every 5 seconds as well.

Please submit here:
https://app4.greenhouse.io/tests/82aa2a45cc1b2791d55f204b14741ef5?utm_medium=email&utm_source=TakeHomeTest

> To deploy your app, you may need to install an [adapter](https://kit.svelte.dev/docs/adapters) for your target environment.

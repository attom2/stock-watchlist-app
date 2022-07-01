

### This project was written as a Take Home Assessment with a 5 day deadline
## To Start
```bash
npm install

npm run dev
```
navigate to http://localhost:3000/


## A Stock Watchlist App
The goal of this app is to provide a way for a user to manage watchlists of quotes.

### Framework Requirements
1. You must use https://svelte.dev/ to develop the front end.
2. You may use a server side framework if you like, it's not required
3. If you choose to use a database, it must be an embedded one like Sqlite
4. Local browser storage is acceptable
5. You should use a modern CSS layout framework such as flexbox or css-grid.
6. You cannot use any jQuery

You should use IEX Cloud’s API to get the current stock quotes: https://www.iexcloud.io/docs/api/#quote

Symbol Search Endpoint
https://vast.tastyworks.com/symbols/search/<Search String>

Example:

https://vast.tastyworks.com/symbols/search/GOO

Application Requirements
1. When the app first starts up, the user should be prompted to enter their username.
    1. A password is not required. User registration and proper authentication should not be done for this exercise.
2. After the user is established, the app should transition to a Watchlist screen.
    1. If this is a new user, a default watchlist should be created for the user
        1. The default watchlist’s name should be “{user’s first name} first list”
        2. The default watchlist should have the following symbols: AAPL, MSFT, SPY
    2. If this is a returning user, the user's previously created watchlists should be loaded
3. The Watchlist screen should consisting of the following elements:
  1. A way to switch watchlists
  2. The watchlist table composed of the following columns:
      1. Stock Symbol
      2. Description
      3. Bid Price
      4. Ask Price
      5. Last Price
  3. A way to modify a watchlist
      1. Add a symbol to an existing watchlist
      2. Remove a symbol from an existing watchlist
      3. Create a new watchlist with a given name
      4. Delete a watchlist
4. When the user is adding a symbol to a watchlist, they should be presented with a symbol search screen to allow for auto-completion of a symbol.
   1. Symbol auto-completion should done using the symbol search endpoint specified above
5. The application should refresh the quotes on the active watchlist every 5 seconds.
6. When a user selects a symbol from a watch list it should bring up a new view that displays the Stock Symbol, Bid, Ask, and Last Price As well as a chart.
   1. The chart should show the last 30 days of the symbol’s last price history.
      1. The stock price history chart should render a point for the average price per historic quote
      2. You can retrieve the last 30 days of data from IEX’s chart api.
      3. Feel free to use an open source library to interact with IEX.
   2. The quote prices should update every 5 seconds as well.


30 day chart:
![image](https://user-images.githubusercontent.com/49999607/176975899-20937c80-4b9b-4e47-9f51-206e872e8510.png)

Watchlist:

![image](https://user-images.githubusercontent.com/49999607/176975931-a65169ce-0be3-4b90-82a6-8b026536deb0.png)

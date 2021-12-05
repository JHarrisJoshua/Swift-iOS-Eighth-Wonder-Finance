# Eighth Wonder Finance

## Table of Contents
* [Overview](#Overview)
* [Product Spec](#Product-Spec)
* [Video Walkthrough](#Video-Walkthrough)
* [Wireframes](#Wireframes)
* [Schema](#Schema)

## Overview
### Description
An application where users can simulate trading stocks with a starting balance of fake money. Users can research companies to find out more information as well as the current share price, review their holdings, and buy/sell shares of stock. Users can change settings, such as dark mode, and reset their hypothetical balance to the starting amount. 

### App Evaluation
- **Category:** Finance / Gaming
- **Mobile:** This app will be developed primarily for mobile devices. Future consideration may be given to a desktop version.
- **Story:** Stock trading game where users can simulate trading stocks with a starting balance of fake money. Users can research stock prices, see the current balance of their holdings, and view their user profile.
- **Market:** Any individual interested in stock trading could download this app and play the game. 
- **Habit:** The app could be used as often as the user wants depending on their appetite for trading stocks and checking in on their balance (i.e. gains and losses). 
- **Scope:** Our first objective is to allow users to research stock prices using the ticker symbol for a publicly traded company. This could evolve into a stock trading game with users competing in seasonal tournaments for prizes, similar to fantasy football apps. Large potential for use with brokerage accounts, electronic trading platforms, or social media platforms.  

## Product Spec

### 1. User Stories

**User Stories**

* User can see current price of stocks as well as a logo of the company
* User can research stock information by ticker symbol or company name
* User can purchase shares of stock using a hypothetical balance of cash
* User can sell shares of stock, and balance will reflect realized gains and losses
* User can view the current balance of their portfolio

**Quality of Life Stories**

* Ability to search for stock information using auto-complete based on ticker symbol or company name
* User can toggle light and dark mode

### 2. Screen Archetypes

* Login Screen
  * User must log in to use the application
  * User can sign up for an account
* Home Screen
  * User can view the current balance of their portfolio
  * User can select a stock to segue to the trade view
* Search Screen
  * User can research stock information by ticker symbol or company name
  * User can add an investment to their watchlist
  * User can select a stock to get more information
  * User can click the Trade button to segue to the trade view
* Explore Screen
  * User can explore a list of companies, either from their watchlist, or from a list based on different metrics 
  * User can toggle different metrics such as biggest daily gainers or losers, and see stocks for that metric  
  * User can select a company to view more information as well as purchase/sell shares
* Trade View
  * User can see a logo of the company, the current price of the stock, and the number of shares they own 
  * User can buy/sell shares of stock and view their balance 
* User Profile/Settings Screen
  * User can reset the game
  * User can toggle dark mode
  * User can logout

### 3. Navigation

**Tab Navigation** (Tab to Screen)

* Home tab
* Search tab
* Explore tab
* Profile/Settings tab

**Flow Navigation** (Screen to Screen)

* Login -> Home
* Home -> User can see their holdings and current balances
* Search -> User can search for and segue to the Trade view to buy or sell shares of a stock
* Explore - > User can view stock information for several companies and select one to segue to the Trade view
* Profile - > User can log-out, toggle dark mode, or reset the game

## Video Walkthrough

Here's a walkthrough of implemented user stories:

![8thWonder3](https://user-images.githubusercontent.com/81477294/142957237-acae88c0-3677-4450-baa1-e9feaa6aa878.gif)

![8thWonder4](https://user-images.githubusercontent.com/81477294/142957260-0248cf4d-3830-4c4e-b926-6cbf8b9545b7.gif)

![Eighth Wonder](https://user-images.githubusercontent.com/81477294/140847262-4ca36c53-b9fa-4532-b6e2-f4490665ef06.gif)

![Eighth Wonder](https://user-images.githubusercontent.com/81477294/140848737-259ae235-9461-44a5-b161-5fca96b52859.gif)

## Wireframes (Figma)

### Digital Wireframes & Mockups

<img src="https://user-images.githubusercontent.com/81477294/139751047-1ab9de59-8600-49e9-ba55-dddfc30718b3.png" width=600>

### Interactive Prototype

![ezgif com-gif-maker](https://user-images.githubusercontent.com/81477294/139752630-66ae738b-9660-481b-b3dd-95d8c8ced710.gif)

## Schema 

### Models
#### User

   | Property      | Type     | Description |
   | ------------- | -------- | ------------|
   | objectId      | String   | unique id for the User (default field) |
   | username      | String   | username |
   | password      | String   | password |
   | balance       | Number   | balance of cash in users account |
   | holdings      | Array of StockSnapshots | stocks currently held by user |
   | trades        | Array of Trades | array of trades made by user |
   | profileImage  | File     | image that user uploads |

#### StockSnapshot

   | Property      | Type     | Description |
   | ------------- | -------- | ------------|
   | objectId      | String   | unique id for the StockSnapshot (default field) |
   | user          | Pointer to User | user that holds this stock |
   | symbol        | String   | stock symbol |
   | price         | Number   | stock price at time of creation |
   | quantity      | Number   | stock quantity held by user |
   | createdAt     | DateTime | time StockSnapshot was created (default field) |

#### Trade

   | Property      | Type     | Description |
   | ------------- | -------- | ------------|
   | objectId      | String   | unique id for the Trade (default field) |
   | user          | Pointer to User | user that made the trade |
   | stock         | Pointer to StockSnapshot | stock snapshot |
   | tradeType     | String | Buy or Sell |
   | quantity      | Number   | Number of stocks bought or sold  |
   | sellPrice     | Number   | Price of the stock when sold  |
   | createdAt     | DateTime | time Trade was created (default field) |

### Networking
- Home View
   - (Read/GET) Query all StockSnapshots where user is currentUser
      ```swift
      let query = PFQuery(className:"StockSnapshot")
      query.whereKey("user", equalTo: currentUser)
      query.order(byDescending: "symbol")
      query.findObjectsInBackground { (stocks: [PFObject]?, error: Error?) in
         if let error = error { 
            print(error.localizedDescription)
         } else if let stocks = stocks {
            print("Successfully retrieved \(stocks.count) stock snapshots for \(currentUser).")
         // TODO: Calculate each symbols stock quantity and total cost to purchase
         }
      }
      ```
   - (Read/GET) Query API for each symbol's current price and calculate users current value held
- Sell View
   - (Create/POST) Create a trade (or trades) of stock snapshots
   - (Read/GET) Get stock logo from API
   - (Update/PUT) Update StockSnapshot quantities
   - (Update/PUT) Remove from User.holdings is StockSnapshot.quantity is zero
- Buy View
   - (Create/POST) Create a StockSnapshot
   - (Read/GET) Get stock logo from API
   - (Create/POST) Create a Trade
   - (Update/PUT) Add StockSnapshot and Trade to user
- Research View
   - (Read/GET) Top stocks from API
   - (Read/GET) Get logos for top stocks in API
   - (Read/GET) Stock searched for in API (to see if it exists)
- Research Detail View
   - (Read/GET) Stock information from API
- Profile View
   - (Read/GET) Query user information
      ```swift
      let query = PFQuery(className:"User")
      query.whereKey("objectId", equalTo: currentUser)
      query.includeKeys(["trades", "trades.stock"])
      query.order(byDescending: "createdAt")
      query.findObjectsInBackground { (stocks: [PFObject]?, error: Error?) in
         if let error = error { 
            print(error.localizedDescription)
         } else if let trades = trades {
            print("Successfully retrieved \(trades.count) stock snapshots for \(currentUser).")
         // TODO: Display trades made by user
         }
      }
      ```


##### IEX Cloud API 
- Base URL - [https://cloud.iexapis.com/stable](https://cloud.iexapis.com/stable)

   HTTP Verb | Endpoint | Description | Credits
   ----------|----------|------------|------------
    `GET`    | /stock/{symbol}/quote | current price, name and stock info | 1
    `GET`    | /stock/{symbol}/company | company description paragraph and info | 10
    `GET`    | /stock/market/list/{list-type} | list of 10 quotes based on list-type (mostactive, gainers, losers) | ~10
    `GET`    | /stock/{symbol}/logo | logo for symbol | 1


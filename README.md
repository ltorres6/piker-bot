# Piker Bot

Stock trading bot for executing swing trades over days to weeks. 

## Features
- Reads queued trades from a Google spreadsheet in my Google drive and adds them to local sqlite3 database
- Heartbeat pulses every minute and checks the price on stocks being traded.
- Bot purchases the stock when four conditions are met
	- Price has entered into entry price range, at least, once and then, moved out of it.
	- Price moves above SMA3, signaling the start of an uptrend.
	- RSI is below 40.
- If the price moves into the entry range and falls below the stop loss without shifting into an uptrend, the trade is cancelled.
- Bot will sell the stock when multiple conditions are met
	- First, the price must move above the entry price.
	- Then, the RSI must go above 70
	- Or price moves below SM3, signalling the start of a downtrend 
- OR bot will sell the stock 15 minutes before close if the trade is marked with the sell at end of day flag. 
	- The bot will not enter into trades marked for sale at the end of the day within a hour of market closing.
- Shares to purchase is calculated at the time of the sale, based on how many can be purchased using a percentage of the total brokerage account.
- As the trade progreses, the bot automatically updates the sqlite3 database and the trade journal in Google Drive.

## Libraries
- numpy
- alpaca_trade_api
- ezsheets
- schedule
- beaker
- stockstats

## Configuration and Installation
- Clone the [Stock Library](https://github.com/adam-long-tech/stock-libraries) repo and follow the README instructions to install locally with pip.
- Clone the piker-bot into the same parent directory containing stock-libaries.
- Copy the example_configuration.py file and rename it bot_configuration.py
- Follow the notes in bot_configuration.py to configure the bot properly.
- The Google Drive API will provide instructions on how to activate the API on a first run.

## Running the Bot

### Docker Image
Run from the parent directory containing the project folder and the stock-libraries module. 
You need to have run it manually once to setup the credentials files for your Google Drive in the piker-bot folder.
Be sure to alter the data folder in bot_configuration.py to point to the mounted folder for the container prior to building.

`docker build -f piker-bot/Dockerfile -t piker-bot .`

`docker run --name piker_bot -d -v [PATH_TO_YOUR_DATA_FOLDER]:/var/lib/piker-bot piker-bot:latest`
`docker run --name piker_bot -d -v d:/development/docker-data:/var/lib/piker-bot piker-bot:latest`

### Running the Scripts Manually.

You can execute main-pulse.py to fire the heartbeat pulse onxw or run main-scheduler.py to activate
the scheduler and begin pulsing the heartbeat every minute for an eternity.


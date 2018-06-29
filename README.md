cryptonote-nodejs-pool
======================

This pool is based on DVANDAL cryptonote-nodejs-pool. For any GELDUM specific question, please head to 
* [Geldum website](http://www.geldum.org)

If you have pool specific questions, please head to
* [GitHub Issues](https://github.com/dvandal/cryptonote-nodejs-pool/)

High performance Node.js (with native C addons) mining pool for CryptoNote based coins. Comes with lightweight example front-end script which uses the pool's AJAX API. Support for Cryptonight (Original, Monero v7, Stellite v7), Cryptonight Light (Original, Aeon v7, IPBC) and Cryptonight Heavy (Sumokoin) algorithms.


#### Table of Contents
* [Features](#features)
* [Community Support](#community--support)
* [Pools Using This Software](#pools-using-this-software)
* [Usage](#usage)
  * [Requirements](#requirements)
  * [Downloading & Installing](#1-downloading--installing)
  * [Configuration](#2-configuration)
  * [Starting the Pool](#3-start-the-pool)
  * [Host the front-end](#4-host-the-front-end)
  * [Customizing your website](#5-customize-your-website)
  * [SSL](#ssl)
  * [Upgrading](#upgrading)
* [JSON-RPC Commands from CLI](#json-rpc-commands-from-cli)
* [Monitoring Your Pool](#monitoring-your-pool)
* [Donations](#donations)
* [Credits](#credits)
* [License](#license)


Features
===

#### Optimized pool server
* TCP (stratum-like) protocol for server-push based jobs
  * Compared to old HTTP protocol, this has a higher hash rate, lower network/CPU server load, lower orphan
    block percent, and less error prone
* Support for Cryptonight (Original, Monero v7, Stellite v7), Cryptonight Light (Original, Aeon v7, IPBC) and Cryptonight Heavy (Sumokoin) algorithms.
* IP banning to prevent low-diff share attacks
* Socket flooding detection
* Share trust algorithm to reduce share validation hashing CPU load
* Clustering for vertical scaling
* Ability to configure multiple ports - each with their own difficulty
* Miner login (wallet address) validation
* Workers identification (specify worker name as the password)
* Variable difficulty / share limiter
* Set fixed difficulty on miner client by passing "address" param with "+[difficulty]" postfix
* Modular components for horizontal scaling (pool server, database, stats/API, payment processing, front-end)
* SSL support for both pool and API servers
* RBPPS (PROP) payment system

#### Live statistics API
* Currency network/block difficulty
* Current block height
* Network hashrate
* Pool hashrate
* Each miners' individual stats (hashrate, shares submitted, pending balance, total paid, payout estimate, etc)
* Blocks found (pending, confirmed, and orphaned)
* Historic charts of pool's hashrate, miners count and coin difficulty
* Historic charts of users's hashrate and payments

#### Mined blocks explorer
* Mined blocks table with block status (pending, confirmed, and orphaned)
* Blocks luck (shares/difficulty) statistics

#### Smart payment processing
* Splintered transactions to deal with max transaction size
* Minimum payment threshold before balance will be paid out
* Minimum denomination for truncating payment amount precision to reduce size/complexity of block transactions
* Prevent "transaction is too big" error with "payments.maxTransactionAmount" option
* Option to enable dynamic transfer fee based on number of payees per transaction and option to have miner pay transfer fee instead of pool owner (applied to dynamic fee only)
* Control transactions priority with config.payments.priority (default: 0).
* Set payment ID on miner client when using "[address].[paymentID]" login
* Integrated payment ID addresses support for Exchanges

#### Admin panel
* Aggregated pool statistics
* Coin daemon & wallet RPC services stability monitoring
* Log files data access
* Users list with detailed statistics

#### Pool stability monitoring
* Detailed logging in process console & log files
* Coin daemon & wallet RPC services stability monitoring
* See logs data from admin panel

#### Extra features
* An easily extendable, responsive, light-weight front-end using API to display data
* Onishin's [keepalive function](https://github.com/perl5577/cpuminer-multi/commit/0c8aedb)
* Support for slush mining system (disabled by default)
* E-Mail Notifications on worker connected, disconnected (timeout) or banned (support MailGun, SMTP and Sendmail)
* Telegram channel notifications when a block is unlocked
* Top 10 miners report
* Multilingual user interface


Community / Support
===

* [GitHub Wiki](https://github.com/dvandal/cryptonote-nodejs-pool/wiki)
* [GitHub Issues](https://github.com/dvandal/cryptonote-nodejs-pool/issues)
* [Geldum Telegram Group](http://t.me/geldum)
* [Telegram Group](http://t.me/CryptonotePool)

#### Pools Using This Software

* http://www.geldumpool.com
* http://geldum.poolmining.xyz/
* http://youpool.io/GDM/
* http://superblockchain.con-ip.com/gdm/


Usage
===

#### Requirements
* Coin daemon(s) (find the coin's repo and build latest version from source)
  * [List of Cryptonote coins](https://github.com/dvandal/cryptonote-nodejs-pool/wiki/Cryptonote-Coins)
* [Node.js](http://nodejs.org/) v4.0+
  * For Ubuntu: 
 ```
  curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash
  sudo apt-get install -y nodejs
```
* [Redis](http://redis.io/) key-value store v2.6+ 
  * For Ubuntu: 
```
sudo add-apt-repository ppa:chris-lea/redis-server
sudo apt-get update
sudo apt-get install redis-server
 ```
* libssl required for the node-multi-hashing module
  * For Ubuntu: `sudo apt-get install libssl-dev`

* Boost is required for the cryptoforknote-util module
  * For Ubuntu: `sudo apt-get install libboost-all-dev`


##### Seriously
Those are legitimate requirements. If you use old versions of Node.js or Redis that may come with your system package manager then you will have problems. Follow the linked instructions to get the last stable versions.


[**Redis warning**](http://redis.io/topics/security): It'sa good idea to learn about and understand software that
you are using - a good place to start with redis is [data persistence](http://redis.io/topics/persistence).

#### 1) Downloading & Installing


Clone the repository and run `npm update` for all the dependencies to be installed:

```bash
git clone https://github.com/dvandal/cryptonote-nodejs-pool.git pool
cd pool

npm update
```

#### 2) Configuration

Use `config_geldum.json` then overview each options and change any to match your preferred setup.

Explanation for each field:
```javascript
/* Pool host displayed in notifications and front-end */
"poolHost": "your.pool.host",

/* Used for storage in redis so multiple coins can share the same redis instance. */
 "coin": "geldum",

/* Used for front-end display */
 "symbol": "GDM",

/* Minimum units in a single coin, see COIN constant in DAEMON_CODE/src/cryptonote_config.h */
 "coinUnits": 100000000000,

/* Number of coin decimals places for notifications and front-end */
"coinDecimalPlaces": 4,
  
/* Coin network time to mine one block, see DIFFICULTY_TARGET constant in DAEMON_CODE/src/cryptonote_config.h */
"coinDifficultyTarget": 60,

/* Set Cryptonight algorithm settings.
   Supported algorithms: cryptonight (default). cryptonight_light and cryptonight_heavy
   Supported variants for "cryptonight": 0 (Original), 1 (Monero v7), 3 (Stellite / XTL)
   Supported variants for "cryptonight_light": 0 (Original), 1 (Aeon v7), 2 (IPBC)
   Supported blob types: 0 (Cryptonote), 1 (Forknote v1), 2 (Forknote v2), 3 (Cryptonote v2 / Masari) */
    "cnAlgorithm": "cryptonight",
    "cnVariant": 0,
"cnBlobType": 0,

/* Logging */
"logging": {

    "files": {

        /* Specifies the level of log output verbosity. This level and anything
           more severe will be logged. Options are: info, warn, or error. */
        "level": "info",

        /* Directory where to write log files. */
        "directory": "logs",

        /* How often (in seconds) to append/flush data to the log files. */
        "flushInterval": 5
    },

    "console": {
        "level": "info",
        /* Gives console output useful colors. If you direct that output to a log file
           then disable this feature to avoid nasty characters in the file. */
        "colors": true
    }
},

    "poolServer": {
        "enabled": true,
        "clusterForks": "auto",
        "poolAddress": "your__pool__wallet__address",
        "intAddressPrefix": 1738090,
        "blockRefreshInterval": 1000,
        "minerTimeout": 900,
        "sslCert": "./your__cert_file__path",
        "sslKey": "./your__private__key__path",
        "sslCA": "./your__chain__file__path",
        "ports": [
            {
                "port": 3333,
                "difficulty": 5000,
                "desc": "Low end hardware"
            },
            {
                "port": 4444,
                "difficulty": 15000,
                "desc": "Mid range hardware"
            },
            {
                "port": 5555,
                "difficulty": 25000,
                "desc": "High end hardware"
            },
            {
                "port": 7777,
                "difficulty": 500000,
                "desc": "Cloud-mining / NiceHash"
            },
            {
                "port": 8888,
                "difficulty": 25000,
                "desc": "Hidden port",
                "hidden": true
            },
            {
                "port": 9999,
                "difficulty": 20000,
                "desc": "SSL connection",
                "ssl": true
            }
        ],
        "varDiff": {
            "minDiff": 100,
            "maxDiff": 100000000,
            "targetTime": 60,
            "retargetTime": 30,
            "variancePercent": 30,
            "maxJump": 100
        },
        "paymentId": {
            "addressSeparator": "+"
        },
        "fixedDiff": {
            "enabled": true,
            "addressSeparator": "."
        },
        "shareTrust": {
            "enabled": true,
            "min": 10,
            "stepDown": 3,
            "threshold": 10,
            "penalty": 30
        },
        "banning": {
            "enabled": true,
            "time": 600,
            "invalidPercent": 25,
            "checkThreshold": 30
        },
        "slushMining": {
            "enabled": false,
            "weight": 300,
            "blockTime": 60,
            "lastBlockCheckRate": 1
         }
    },

    "payments": {
        "enabled": true,
        "interval": 1800,
        "maxAddresses": 50,
        "mixin": 5,
        "priority": 0,
        "transferFee": 50000000000,
        "dynamicTransferFee": true,
        "minerPayFee" : true,
        "minPayment": 700000000000,
        "maxTransactionAmount": 0,
        "denomination": 100000000000
    },

    "blockUnlocker": {
        "enabled": true,
        "interval": 30,
        "depth": 10,
        "poolFee": 1,
        "devDonation": 0.0,
        "networkFee": 0.0
    },

    "api": {
        "enabled": true,
        "hashrateWindow": 600,
        "updateInterval": 5,
        "bindIp": "your__pool__host__ip",
        "port": 8118,
        "blocks": 30,
        "payments": 30,
        "password": "your__pool__admin__panel__password",
        "ssl": true,
        "sslPort": 8119,
        "sslCert": "./cert.pem",
        "sslKey": "./privkey.pem",
        "sslCA": "./chain.pem",
        "trustProxyIP": true
    },

    "daemon": {
        "host": "127.0.0.1",
        "port": 21937
    },

    "wallet": {
        "host": "127.0.0.1",
        "port": 30888
    },

    "redis": {
        "host": "127.0.0.1",
        "port": 6379,
        "auth": null,
        "db": 0,
        "cleanupInterval": 15
    },

    "notifications": {
        "emailTemplate": "email_templates/default.txt",
        "emailSubject": {
            "emailAdded": "Your email was registered",
            "workerConnected": "Worker %WORKER_NAME% connected",
            "workerTimeout": "Worker %WORKER_NAME% stopped hashing",
            "workerBanned": "Worker %WORKER_NAME% banned",
            "blockFound": "Block %HEIGHT% found !",
            "blockUnlocked": "Block %HEIGHT% unlocked !",
            "blockOrphaned": "Block %HEIGHT% orphaned !",
            "payment": "We sent you a payment !"
        },
        "emailMessage": {
            "emailAdded": "Your email has been registered to receive pool notifications.",
            "workerConnected": "Your worker %WORKER_NAME% for address %MINER% is now connected from ip %IP%.",
            "workerTimeout": "Your worker %WORKER_NAME% for address %MINER% has stopped submitting hashes on %LAST_HASH%.",
            "workerBanned": "Your worker %WORKER_NAME% for address %MINER% has been banned.",
            "blockFound": "Block found at height %HEIGHT% by miner %MINER% on %TIME%. Waiting maturity.",
            "blockUnlocked": "Block mined at height %HEIGHT% with %REWARD% and %EFFORT% effort on %TIME%.",
            "blockOrphaned": "Block orphaned at height %HEIGHT% :(",
            "payment": "A payment of %AMOUNT% has been sent to %ADDRESS% wallet."
        },
        "telegramMessage": {
            "workerConnected": "Your worker _%WORKER_NAME%_ for address _%MINER%_ is now connected from ip _%IP%_.",
            "workerTimeout": "Your worker _%WORKER_NAME%_ for address _%MINER%_ has stopped submitting hashes on _%LAST_HASH%_.",
            "workerBanned": "Your worker _%WORKER_NAME%_ for address _%MINER%_ has been banned.",
            "blockFound": "*Block found at height* _%HEIGHT%_ *by miner* _%MINER%_*! Waiting maturity.*",
            "blockUnlocked": "*Block mined at height* _%HEIGHT%_ *with* _%REWARD%_ *and* _%EFFORT%_ *effort on* _%TIME%_*.*",
            "blockOrphaned": "*Block orphaned at height* _%HEIGHT%_ *:(*",
            "payment": "A payment of _%AMOUNT%_ has been sent."
        }
    },

    "email": {
        "enabled": false,
        "fromAddress": "your@email.com",
        "transport": "sendmail",
        "sendmail": {
            "path": "/usr/sbin/sendmail"
        },
        "smtp": {
            "host": "smtp.example.com",
            "port": 587,
            "secure": false,
            "auth": {
                "user": "username",
                "pass": "password"
            },
            "tls": {
                "rejectUnauthorized": false
            }
        },
        "mailgun": {
            "key": "your-private-key",
            "domain": "mg.yourdomain"
        }
    },

    "telegram": {
        "enabled": false,
        "botName": "GeldumPoolBot",
        "token": "585908539:AAF4XAl_A7Gb4RWxnRWn7pTBAaCKNwKZMgw",
        "channel": "Geldum",
        "channelStats": {
            "enabled": true,
            "interval": 30
        },
        "botCommands": {
            "stats": "/stats",
            "enable": "/enable",
            "disable": "/disable"
        }
    },

    "monitoring": {
        "daemon": {
            "checkInterval": 60,
            "rpcMethod": "getblockcount"
        },
        "wallet": {
            "checkInterval": 60,
            "rpcMethod": "getbalance"
        }
    },

    "prices": {
        "source": "tradeogre",
        "currency": "USD"
    },
    
    "charts": {
        "pool": {
            "hashrate": {
                "enabled": true,
                "updateInterval": 60,
                "stepInterval": 1800,
                "maximumPeriod": 86400
            },
            "miners": {
                "enabled": true,
                "updateInterval": 60,
                "stepInterval": 1800,
                "maximumPeriod": 86400
            },
            "workers": {
                "enabled": true,
                "updateInterval": 60,
                "stepInterval": 1800,
                "maximumPeriod": 86400
            },
            "difficulty": {
                "enabled": true,
                "updateInterval": 1800,
                "stepInterval": 10800,
                "maximumPeriod": 604800
            },
            "price": {
                "enabled": true,
                "updateInterval": 1800,
                "stepInterval": 10800,
                "maximumPeriod": 604800
            },
            "profit": {
                "enabled": true,
                "updateInterval": 1800,
                "stepInterval": 10800,
                "maximumPeriod": 604800
            }
        },
        "user": {
            "hashrate": {
                "enabled": true,
                "updateInterval": 180,
                "stepInterval": 1800,
                "maximumPeriod": 86400
            },
            "payments": {
                "enabled": true
            }
        }
    }
}
```

#### 3) Start the pool

```bash
node init.js
```

The file `config.json` is used by default but a file can be specified using the `-config=file` command argument, for example:

```bash
node init.js -config=config_backup.json
```

This software contains four distinct modules:
* `pool` - Which opens ports for miners to connect and processes shares
* `api` - Used by the website to display network, pool and miners' data
* `unlocker` - Processes block candidates and increases miners' balances when blocks are unlocked
* `payments` - Sends out payments to miners according to their balances stored in redis


By default, running the `init.js` script will start up all four modules. You can optionally have the script start
only start a specific module by using the `-module=name` command argument, for example:

```bash
node init.js -module=api
```

[Example screenshot](http://i.imgur.com/SEgrI3b.png) of running the pool in single module mode with tmux.

To keep your pool up, on operating system with systemd, you can create add your pool software as a service.  
Use this [example](https://github.com/VirtuBox/cryptonote-nodejs-pool/blob/master/utils/cryptonote-nodejs-pool.service) and create your service file `/lib/systemd/system/cryptonote-nodejs-pool.service`
Then enable and start the service with the following commands : 

```
systemctl enable cryptonote-nodejs-pool.service
systemctl start cryptonote-nodejs-pool.service
```

#### 4) Host the front-end

Simply host the contents of the `website_example` directory on file server capable of serving simple static files.


Edit the variables in the `website_example/config.js` file to use your pool's specific configuration.
Variable explanations:

```javascript

var api = "http://geldumpool.com:8117";

var email = "support@poolhost.com";
var telegram = "https://t.me/YourPool";
var discord = "https://discordapp.com/invite/YourPool";

var marketCurrencies = ["{symbol}-BTC", "{symbol}-USD", "{symbol}-EUR", "{symbol}-CAD"];

var blockchainExplorer = "http://chainradar.com/{symbol}/block/{id}";
var transactionExplorer = "http://chainradar.com/{symbol}/transaction/{id}";

var themeCss = "themes/default.css";
var defaultLang = 'en';

```

#### 5) Customize your website

The following files are included so that you can customize your pool website without having to make significant changes
to `index.html` or other front-end files thus reducing the difficulty of merging updates with your own changes:
* `custom.css` for creating your own pool style
* `custom.js` for changing the functionality of your pool website


Then simply serve the files via nginx, Apache, Google Drive, or anything that can host static content.

#### SSL

You can configure the API to be accessible via SSL using various methods. Find an example for nginx below:

* Using SSL api in `config.json`:

By using this you will need to update your `api` variable in the `website_example/config.js`. For example:  
`var api = "https://poolhost:8119";`

* Inside your SSL Listener, add the following:

``` javascript
location ~ ^/api/(.*) {
    proxy_pass http://127.0.0.1:8117/$1$is_args$args;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}
```

By adding this you will need to update your `api` variable in the `website_example/config.js` to include the /api. For example:  
`var api = "http://poolhost/api";`

You no longer need to include the port in the variable because of the proxy connection.

* Using his own subdomain, for example `api.poolhost.com`:

```bash
server {
    server_name api.poolhost.com
    listen 443 ssl http2;

    ssl_certificate /your/ssl/certificate;
    ssl_certificate_key /your/ssl/certificate_key;

    location / {
        more_set_headers 'Access-Control-Allow-Origin: *';
        proxy_pass http://127.0.01:8117;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_cache_bypass $http_upgrade;
    }
}
```

By adding this you will need to update your `api` variable in the `website_example/config.js`. For example:  
`var api = "//api.poolhost.com";`

You no longer need to include the port in the variable because of the proxy connection.


#### Upgrading
When updating to the latest code its important to not only `git pull` the latest from this repo, but to also update
the Node.js modules, and any config files that may have been changed.
* Inside your pool directory (where the init.js script is) do `git pull` to get the latest code.
* Remove the dependencies by deleting the `node_modules` directory with `rm -r node_modules`.
* Run `npm update` to force updating/reinstalling of the dependencies.
* Compare your `config.json` to the latest example ones in this repo or the ones in the setup instructions where each config field is explained. You may need to modify or add any new changes.

### JSON-RPC Commands from CLI

Documentation for JSON-RPC commands can be found here:
* Daemon https://wiki.bytecoin.org/wiki/JSON_RPC_API
* Wallet https://wiki.bytecoin.org/wiki/Wallet_JSON_RPC_API


Curl can be used to use the JSON-RPC commands from command-line. Here is an example of calling `getblockheaderbyheight` for block 100:

```bash
curl 127.0.0.1:18081/json_rpc -d '{"method":"getblockheaderbyheight","params":{"height":100}}'
```


### Monitoring Your Pool

* To inspect and make changes to redis I suggest using [redis-commander](https://github.com/joeferner/redis-commander)
* To monitor server load for CPU, Network, IO, etc - I suggest using [New Relic](http://newrelic.com/)
* To keep your pool node script running in background, logging to file, and automatically restarting if it crashes - I suggest using [forever](https://github.com/nodejitsu/forever) or [PM2](https://github.com/Unitech/pm2)


Donations
---------

Thanks for supporting my works on this project! If you want to make a donation to [Daniel Vandal](https://github.com/dvandal/), the developper of this project, you can send any amount of your choice to one of theses addresses:
This acconuts are for Daniel Vandal, not Geldum, support the original developers.
* Bitcoin (BTC): `17XRyHm2gWAj2yfbyQgqxm25JGhvjYmQjm`
* Monero (XMR): `49WyMy9Q351C59dT913ieEgqWjaN12dWM5aYqJxSTZCZZj1La5twZtC3DyfUsmVD3tj2Zud7m6kqTVDauRz53FqA9zphHaj`

Credits
---------

* [fancoder](//github.com/fancoder) - Developper on cryptonote-universal-pool project from which current project is forked.
* [dvandal](//github.com/dvandal) - Developper on cryptonote-nodejs-pool project from which current Geldum project is forked.
* [nanze](//github.com/nanzee) - Developer who actually mantain the pool.

License
-------
Released under the GNU General Public License v2

http://www.gnu.org/licenses/gpl-2.0.html

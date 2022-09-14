---
description: >-
  This first section will show you how to set up panic_cosmos to alert of any
  missing blocks and lose of peers. It uses RPC to query the server and pull
  data and alert a Telegram Account of your choice.
---

# Panic Cosmos

This first section will show you how to set up panic\_cosmos to alert of any missing blocks and lose of peers. It used RPC to query the server and pull data from it to be alerted to your TG bot.

In this tutorial we are using Linux cloud machine with user set up called `monitoring` and inside there we will place our working directories along with service file for running in background.

Follow the guide from [here](https://github.com/SimplyVC/panic\_cosmos/blob/master/doc/INSTALL\_AND\_RUN.md) if you want to see the work from the builders at Simply\_VC



On your monitoring server if it is a fresh install.&#x20;

```javascript
sudo apt-get update
```



**Requirements**

* Python v3.5.2+ with pip package manager and pipenv packaging tool.
* **Optional**: Telegram account and bots, for Telegram alerts and commands.
* **Optional**: Twilio account, for highly effective phone call alerts.
* **Optional**: Redis server, to keep a backup of the alerter state and to have some control over the alerter, such as to snooze phone call alerts using Telegram commands.

#### Step 1: installing Python (with pip and pipenv)



```javascript
/// create a user
su - username
```

Install Python v3.5.2 or greater:

```javascript
sudo apt-get install python3.8 python3-pip
```

Once the installation is complete, you can run `Python 3.8` with the python3.8 command and pip with the `pip3` command.

```javascript
python3.8 --version
///Python 3.8.4
```

To run the test suite, type the following command:

```javascript
python3.8 -m test
```

To install pip package manager

```javascript
apt-get install python3-pip
```

To install pipenv packaging tool, run

```javascript
pip install pipenv
```

#### Step 2: Telegram: [Click here](https://github.com/SimplyVC/panic\_cosmos/blob/master/doc/INSTALL\_TELEGRAM.md) if you want to set up a Telegram account with bots.



#### Step 3: Setting up PANIC

PANIC requires its own setup process, involving three main parts, each of which generates its own respective config file:

* Linking up any of the optional features that you set up (`config/user_config_main.ini`)
* Providing a list of nodes that you wish to monitor (`config/user_config_nodes.ini`)
* Providing a list of GitHub repositories that you wish to monitor (`config/user_config_repos.ini`)

All of these steps are packaged into one setup process which is started up by running:

```javascript
su - username

pipenv sync

pipenv run python run_setup.py
```

{% hint style="info" %}
If multiple versions of Python are installed, the python executable may be 'python3.6', 'python3.7', etc.
{% endhint %}

Alternatively, you can take a look at the three `config/example_***.ini` files and perform the config file generation manually by copying the three example config files to the ones listed above and replacing the example pieces of information with actual ones.

The setup process is guided by instructions which we highly recommend that you read. The setup of any optional feature that was not set up in the previous section can be skipped. For convenience, any yes/no question can be answered with a _yes_ just by pressing _ENTER_.

Lastly, note that if you wish to change some configurations and run the setup process again, it will detect the config files and will not simply overwrite the current configurations.



#### STEP 4: Advanced Configuration

Edit the user\_config\_main.ini\` file by using nano or vi what ever you prefer and add your TG bot config. for example:

```javascript
[telegram_alerts]
enabled = True
bot_token = <BOT-TOKEN>
bot_chat_id = <CHAT_ID> 
```

{% hint style="info" %}
The chat ID is took from the API that the guide showed you earlier on setting up TG bot.
{% endhint %}

Edit the `user_config_nodes.ini` file now.&#x20;

This is where you can add the node you wish to get alerts for. Multiple nodes can be added here. For example.

<figure><img src="../../../../.gitbook/assets/Screenshot 2022-08-31 at 15.01.11.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
If you are not intending to run sentries for your validator nodes then you need to add the same \<NODE\_RPC\_IP> and set the other to `validator=false` so you have two nodes being queried.
{% endhint %}

On each NODE you wish to monitor set the RPC and add rule in `ufw status` to allow connection from monitoring server

```javascript
sudo ufw allow from <monitoring_node_ip>/32 proto tcp to any port 26657 comment "Panic_Cosmos"
```

#### Step 5: Running PANIC

After all of the setting-up, you will be glad to find out that running the alerter is a breeze. To start up PANIC simply run the following commands:

```javascript
pipenv sync
pipenv run python run_alerter.py
# If multiple versions of Python are installed, the python executable may be `python3.6`, `python3.7`, etc.
```



Set up service for your session to run.

```javascript
nano /etc/systemd/system/panic_alerter.service

[Unit]
Description=PANIC
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
User=username
TimeoutStopSec=90s
WorkingDirectory=/home/username/panic_cosmos/
ExecStart=/usr/bin/pipenv run python /home/panic_cosmos/run_alerter.py

[Install]
WantedBy=multi-user.target
```

```javascript
systemctl daemon-reload

systemctl start panic_alerter

journalctl -u panic_alerter -f
```

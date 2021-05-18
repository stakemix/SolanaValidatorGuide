# SolanaValidator-OnePage Installation Guide 
### Do not install your software in root. Its a security risk
This is one way of setting up the solana validator. Please refer to solana documentation for latest instructions
- Unlike many other blockchain networks, Solana needs beefier hardware
- Good to have a solid good background in unix systems and software management
- Share edits / corrections / suggestions in Github or with authors ( support@stakemix.com )

### Guide helps you to build Solana validator on Ubuntu 20.04.  Other  unix variants may need command translation.

## Hope you are familiar with basic Unix administration & commands
Learn about Linux and a few key commands.  You will find enough tutorials on google. 
- introduction to linux
- su, sudo, mkdir, top, tail, export, echo
- vi or nano editor

## Lets first update your server & Install a firewall
( More detailed instructions for securing your server are in the [security guide](SecureYourValidator.md) soon.)

###### Upgrade all your software and install UFW ( firewall )
```
$ sudo apt update
$ sudo apt upgrade
$ sudo apt install ufw
```
###### Enable your firewall
- UFW is a universal firewall that is available for free. This can help you protect your services from being attacked by random people on the internet.  While the topic of a firewall is very broad, the following commands should help lock down your server to just the ssh port and avoid any additional vulnerabilities from being exploited
```
$ sudo apt install ufw
$ sudo ufw status
$ sudo ufw allow 22/tcp
$ sudo ufw enable 
$ sudo ufw status
```
## Solana Setup &  Installation

### Create a dedicated User
**DO NOT install solana software as Root. **
```
$ sudo adduser solana #( follow the prompts )
$ sudo usermod -aG sudo solana #( adds the user to sudo group )
```
### Install Solana Software
```
$ su - solana #Login into solana user
$ sh -c "$(curl -sSfL https://release.solana.com/beta/install)"
$ exit #( you should be back in ubuntu user )
$ su - solana # Login again into solana user 
$ solana --version #( you should see the version of the software you installed )
$ solana-install info #( Shows whether you have the lastest version or if you need to upgrade )
$ solana-install update #( Updates your software to the latest release )
```
### Open ports on your firewall to allow solana cluster communication
```
$ sudo ufw allow 8001:8012/tcp
$ sudo ufw allow 8001:8012/udp
$ sudo ufw allow 8899/tcp
```

### Generate your keys
you need atleast 2 keys to run a solana instance.  Identification Key & Voting Key<br/>
**Its important that you save all the key mnemonics in an offline system.. ( Hardware wallet, Pen/Paper..Dont loose these )"**
```
$ solana-keygen new -o ~/.config/solana/id.json  #( this is your identification key )
$ solana-keygen new -o ~/.config/solana/vote.json  #( this is your voting key )
```

### Configure your preferred cluster
Solana has 3 clusters - devnet, testnet and mainnet. We recommend that you use the devnet or testnet cluster for your first setup<br/>
``` 
$ solana config set --url https://testnet.solana.com 
$ export SOLANA_METRICS_CONFIG="host=https://metrics.solana.com:8086,db=tds,u=testnet_write,p=c4fa841aa918bf8274e3e2a44d77568d9861b3ea" 
$ solana transaction-count #( confirm that the cluster is reachable )
```
### Set your identity key
```
$ solana config set --keypair ~/.config/solana/id.json #( sets your identity key file for all commands ) 
$ solana balance #( shows the balance in your identity wallet )
$ solana airdrop 1 ( adds 1 testnet sol to your wallet )
$ solana balance ( should display the balance of 1 testnet solana )
```
### Create your vote account
```
$ solana create-vote-account ~/.config/solana/vote.json ~/.config/solana/id.json
```
### Tune your unix kernel
https://docs.solana.com/running-validator/validator-start#system-tuning

### Startup your validator
```
$ mkdir /home/solana/bin #(Create a directory to hold your scripts )
```
Create a script called solana-start.sh in /home/solana/bin.  Use a text editor and place this file in the bin directory.
Please note that we simplified all directory setups below to be in root disk. 
Please change the ledger and log directory or soft link it at ~/ledger and ~/log
```
#!/bin/bash
# solana-start.sh for starting the solana validator
set -x
cd /home/solana
source /home/solana/.profile
export SOLANA_METRICS_CONFIG="host=https://metrics.solana.com:8086,db=tds,u=testnet_write,p=c4fa841aa918bf8274e3e2a44d77568d9861b3ea"
solana config set --url https://testnet.solana.com

solana-validator \
    --identity ~/.config/solana/id.json \
    --vote-account ~/.config/solana/vote.json \
    --trusted-validator 7XSY3MrYnK8vq693Rju17bbPkCN3Z7KvvfvJx4kdrsSY \
    --trusted-validator Ft5fbkqNa76vnsjYNwjDZUXoTWpP7VYm3mtsaQckQADN \
    --trusted-validator 9QxCLckBiJc783jnMvXZubK4wH86Eqqvashtrwvcsgkv \
    --no-untrusted-rpc \
    --ledger ~/ledger \
    --log ~/log/validator.log \
    --rpc-port 8899 \
    --private-rpc \
    --dynamic-port-range 8001-8012 \
    --entrypoint testnet.solana.com:8001 \
    --expected-genesis-hash 4uhcVJyU9pJkvQyS88uRDiswHXSCkY3zQawwpjk2NsNY \
    --wal-recovery-mode skip_any_corrupted_record \
    --limit-ledger-size 350000000
```
Create a script named solana-stop.sh in /home/solana/bin using vi or nano
```
#!/bin/bash
#solana-stop.sh for stopping the validator using exit command
#set -x
source /home/solana/.profile
cd /home/solana
export SOLANA_METRICS_CONFIG="host=https://metrics.solana.com:8086,db=tds,u=testnet_write,p=c4fa841aa918bf8274e3e2a44d77568d9861b3ea"
solana config set --url https://testnet.solana.com
solana-validator --ledger ~/ledger exit --min-idle-time 15
```
- Save the file and add x bit
 ```
- sudo chmod +x /home/solana/solana-start.sh
- sudo chmod +x /home/solana/solana-stop.sh
```
- Now you are ready to start your validator
- Lets run the script in an interactive mode 
-  ```$ ./solana-start.sh ```
- open another terminal, ```su - solana``` and tail your log to see progress ```$ tail -f ~/validator.log```
- You will see a lot of activity
- ctrl+c and come back to the command prompt 
- ```solana-validator --ledger ~/ledger monitor``` # this command will help you monitor the progress. 

### Configure your validator as a system service
Copy this to a file name /etc/systemd/system/solana.service ( you will need sudo or root to save here )
```
[Unit]
Description=Solana Validator
After=network.target
StartLimitIntervalSec=180
StartLimitBurst=3

[Service]
Type=simple
Restart=always
RestartSec=30
User=solana
LimitNOFILE=700000
LogRateLimitIntervalSec=0
Environment="PATH=/bin:/usr/bin:/home/solana/.local/share/solana/install/active_release/bin"
ExecStart=/home/solana/bin/solana-start.sh
ExecStop=/home/solana/bin/solana-stop.sh

[Install]
WantedBy=multi-user.target
``` 
After you save this file, you should now enable the service and start solana using systemctl file.  
```
$ su - solana
$ sudo systemctl enable solana # enables the service from the file we saved above
$ sudo systemctl start solana  # starts validator,  use stop if you want to stop your validator
```

### Optional : Scripts and Aliases to ease the management of your validator
I am a lazy systems admin so I use a lot of aliases to ease management.  Here are a few you can add to your .bashrc_aliases or .profile to make them work
```
alias soldisable='sudo systemctl disable --now solana' # disable solana autostart
alias solenable='sudo systemctl enable --now solana' # enable solana autostart
alias solstart='sudo systemctl start --now solana' #start solana
alias solstop='sudo systemctl stop --now solana' #stop solana ( waiting for exit to work better )

alias sollog='tail -f ~/log/validator.log' #tail my solana log
alias solmon='solana-validator --ledger ~/ledger monitor' #monitor solana-validator
alias solstake='solana stakes ~/.config/solana/vote.json' #check my stakes
alias solvote='solana vote-account ~/.config/solana/vote.json' #check my vote account & credits
alias syslog='sudo tail -f /var/log/syslog' #tail syslog
```
####Coming soon
- Other Useful Resources
  - Solana Validator FAQ
  - Solana Validator Docs

Content contributions are always welcome! Please take a look at the [contribution guidelines](CONTRIBUTING.md) first.

# SolanaValidator-OnePage Guide 
This is just one way of setting up the solana validator. Please follow the guidance in the official solana documentation.  

- Unlike many other blockchain networks, Solana needs a deeper understanding of systems and software.  
- Its best that you have some background in Unix Sytems Administration to be successful in this edeavour
- Share edits / corrections / suggestions in Github or with authors on Discord ( DfinityUB#1211 or StakeMixAB#9519 )

### Guide is to run a Solana validator on Ubuntu 20.04.  Other variants of unix may need command translation.

## Recommended Hardware & Hosting Specs
To get started on the testnet, you will need the following
- A hosted or cloud server ( Home hosted servers tend to lag in catchup due to network latency )
- with 16+ threads / vcpus
- 64 to 128 GB Ram ( Recommend minimum of 256 GB for Mainnet )
- High IO SSD Disks / NVME Disks ( NVME may not sustain for too long due to the intensity of writes ) 

## Lets harden your server first
The first thing to do when you receive your virtual machine or server is to harden the server to cover the basics. While server hardening is a much broader topic, we recommend atleast the following steps to ensure that your server is protected from unnecessary intrusions.
###### Upgrade all your software and ensure you have UFW ( firewall ) installed
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
$ sudo passwd -d solana #( disable password based login for solana user )
```
### Install Solana Software
```
$ su - solana #Login into solana user
$ sh -c "$(curl -sSfL https://release.solana.com/stable/install)"
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
**Its important that you save all the key mnemonics in an offline system.. Preferably write them on a piece of paper"**
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
$ solana aidrop 1 ( adds 1 testnet sol to your wallet )
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
$ mkdir ~solana/bin #(Create a directory to hold your scripts )
```
Create a script called solana-start.sh in the above directory.  Use a text editor and place this file in the bin directory.
Please note that we simplified all directory setups below to be in root disk. 
Please change the ledger and log directory or soft link it at ~/ledger and ~/log
```
#!/bin/bash
set -x
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
    --log ~/validator.log
    --rpc-port 8899 \
    --private-rpc \
    --dynamic-port-range 8001-8012 \
    --entrypoint testnet.solana.com:8001 \
    --expected-genesis-hash 4uhcVJyU9pJkvQyS88uRDiswHXSCkY3zQawwpjk2NsNY \
    --wal-recovery-mode skip_any_corrupted_record \
    --limit-ledger-size 350000000
```
- Save the file and ```chmod +x start-solana.sh```
- Now you are ready to start your validator
- Lets run the script in an interactive mode  ```$ ./solana-start.sh ```
- open another terminal, ```su - solana``` and tail your log to see progress ```$ tail -f ~/validator.log```
- You will see a lot of activity
- come back to the command prompt 
- ```solana-validator --ledger ~/ledger monitor``` # this command will help you monitor the progress. 

### Configure your validator to start in the background
####Coming soon
### Scripts and Aliases to ease the management of your validator
####Coming soon
- Other Useful Resources
  - Solana Validator FAQ
  - Solana Validator Docs

Contributions are always welcome! Please take a look at the [contribution guidelines](CONTRIBUTING.md) first.

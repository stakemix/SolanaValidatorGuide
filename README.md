# SolanaValidatorGuide
A curated list of resources and simplified content to help everyone come onboard as a Solana Validator. Unlike many other blockchain networks, Solana needs a deeper understanding of systems and software.  Its best that you have some background in Unix Sytems Administration, writing unix scripts and ability to use guidance below on a unix server.

### Guide is to run a validator on Ubuntu 20.04.  Other variants of unix may need command translation.

## Introduction to Solana
## Recommended Hardware & Hosting Specs
## Solana Validator Setup
### Lets harden your server first
The first thing to do when you receive your virtual machine or server is to harden the server atleast to cover the basics. While server hardening is a much broader topic, we recommend atleast the following steps to ensure that your server is protected from unnecessary intrusions.
###### Upgrade all your software and ensure you have UFW ( firewall ) installed
- Operating System : Ubuntu
```
$ sudo apt update
$ sudo apt upgrade
$ sudo apt install ufw
```
###### Enable your firewall
- Operating System : Any
- UFW is a universal firewall that is available for free. This can help you protect your services from being attacked by random people on the internet.  While the topic of a firewall is very broad, the following commands should help lock down your server to just the ssh port and avoid any additional vulnerabilities from being exploited
```
$ sudo apt install ufw
$ sudo ufw status
$ sudo ufw allow 22/tcp
$ sudo ufw enable 
$ sudo ufw status
```
#### Create a dedicated User
- Operating System : Any
- UFW is a universal firewall that is available for free. This can help you protect your services from being attacked by random people on the internet.  While the topic of a firewall is very broad, the following commands should help lock down your server to just the ssh port and avoid any additional vulnerabilities from being exploited
```
$ sudo adduser solana ( follow the prompts )
$ sudo usermod -aG sudo solana ( adds the user to sudo group )
$ sudo passwd -d solana ( disable password based login for solana user )
```
#### Install Solana Software
```
$ sh -c "$(curl -sSfL https://release.solana.com/stable/install)"
$ exit ( you should be back in ubuntu user )
$ su - solana
$ solana --version ( you should see the version of the software you installed )
$ solana-install --info ( Shows whether you have the lastest version or if you need to upgrade )
$ solana-install update ( Updates your software to the latest release )
```
#### Open ports on your firewall to allow solana cluster communication
```
$ ufw allow 8001-8012/tcp
$ ufw allow 8001-8012/udp
$ ufw allow 8899/tcp
```

#### Generate your keys
you need atleast 2 keys to run a solana instance.  Identification Key,  Voting Key, Staking Key<br/>
**Its important that you save all the key mnemonics in an offline system.. Preferably write them on a piece of paper"**
```
$ solana-keygen new -o ~/.config/solana/id.json  ( this is your identification key )
$ solana-keygen new -o ~/.config/solana/vote.json  ( this is your voting key )
$ solana-keygen new -o ~/.config/solana/stake.json  ( this is your staking key )
```

#### Configure your preferred cluster
Solana has 3 clusters - devnet, testnet and mainnet. If you are using this guide, we recommend that you use the testnet cluster for your first setup
``` solana config set --url https://testnet.solana.com ```

- Open Solana ports in your firewall
- Recommended startup script for Testnet
- Startup your validator
- Configure the Validator to auto start in Unix
- Tips : Aliases, Tips, Commands that are useful for validators
- Other Useful Resources
  - Solana Validator FAQ
  - Solana Validator Docs

#### What is a Validator ?
> "If you are here, I am sure you know what this is.. this content is not the most important.. So we will add it later.
> 

Contributions are always welcome! Please take a look at the [contribution guidelines](CONTRIBUTING.md) first.

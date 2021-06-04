## Cheatsheet to Sign up for Solana Foundation New Delegation Program
- Program Details Here ( https://forums.solana.com/t/evolution-of-the-solana-foundation-delegation-program/1906 )
#### This involves transferrring real SOL to your keys.. So please be careful and validate the commands yourself.  
#### No warranties to the commands and steps below
#### Assumption is that you already have solana running on testnet

## Install the libraries necessary to build & run solana-foundation-delegation-program
```
curl https://sh.rustup.rs -sSf | sh
source $HOME/.cargo/env
rustup component add rustfmt
rustup update
sudo apt-get install libssl-dev libudev-dev pkg-config zlib1g-dev llvm clang make
```

## Install the CLI for foundation delegation
```
cargo install solana-foundation-delegation-program-cli
solana-foundation-delegation-program --version
```

## Now create a key for mainnet
```solana-keygen new -o /home/solana/.config/mainnet-id.json```
- Save this key, mnemonic and everything in a different environment #
- Write down the pubkey for use later in the doc <mainnetpubkey>
- dont go to the next step until you save these files and mnemonic etc.#

## You need some real SOL to continue with the next steps
- Buy solana at any of your favourite exchange or wallet
- Transfer 0.25 solana from your exchange to the <mainnetpubkey>
- check if the balance has arrived
```
solana -um balance <mainnetpubkey>  
```
now get the paths to your keys and keep them ready
- <testnetkey> - /home/solana/config/id.json
- <mainnetkey> - /home/solana/config/mainnnet-id.json

## run the application command
```
solana-foundation-delegation-program apply --mainnet <mainnetkey> --testnet <testnetkey> --keypair <mainnetkey>
```
- Check the key values printed back
- Now confirm the application
```
  solana-foundation-delegation-program apply --mainnet <mainnetkey> --testnet <testnetkey> --keypair <mainnetkey>
```
##Check Status  
```
solana-foundation-delegation-program status <mainnetkey>
```
  
##You are done

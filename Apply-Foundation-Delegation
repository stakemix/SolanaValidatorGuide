##Applying to Solana Foundation Delegation 
####This involves transferrring real SOL to your keys.. So please be careful and validate the commands yourself.  
####No warranties to the commands and steps below

#Install the libraries necessary to build & run solana-foundation-delegation-program
curl https://sh.rustup.rs -sSf | sh
source $HOME/.cargo/env
rustup component add rustfmt
rustup update
sudo apt-get install libssl-dev libudev-dev pkg-config zlib1g-dev llvm clang make

# Now create a key for mainnet
solana-keygen new -o /home/solana/.config/mainnet-id.json
# Save this key, mnemonic and everything in a different environment #
# Note down the pubkey <mainnetpubkey>
# dont go to the next step until you save these files and mnemonic etc.#

# You need some real SOL to get to solana
# Buy solana at any of your favourite places
# Transfer 0.25 solana from your favourite wallet or exchange to the <mainnetpubkey>

solana -um balance <mainnetpubkey>  #check if the balance arrived

#now get the paths to your keys and keep them ready
#<testnetkey> - /home/solana/config/id.json
#<mainnetkey> - /home/solana/config/mainnnet-id.json

# run the apply command
solana-foundation-delegation-program apply --mainnet <mainnetkey> --testnet <testnetkey> --keypair <mainnetkey>
solana-foundation-delegation-program status <mainnetkey>


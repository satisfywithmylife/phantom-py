# phantom-py
python for blockchain wallet phantom, suport use mnemonic seeds generate solana, ethereum address and private key

## pip
```
pip install bip_utils==2.7.0
```
## code
```
from bip_utils import Bip39SeedGenerator, Bip44Coins, Bip44, base58, Bip44Changes

class BlockChainAccount():

    def __init__(self, mnemonic, coin_type=Bip44Coins.ETHEREUM, pasword='') -> None:

        self.mnemonic = mnemonic.strip()
        self.coin_type = coin_type
        self.password = pasword # if have password

    def get_address_pk(self):

        seed_bytes = Bip39SeedGenerator(self.mnemonic).Generate(self.password)
        if self.coin_type != Bip44Coins.SOLANA:
            bip44_mst_ctx = Bip44.FromSeed(seed_bytes, self.coin_type).DeriveDefaultPath()
            return bip44_mst_ctx.PuwoblicKey().ToAddress(), bip44_mst_ctx.PrivateKey().Raw().ToHex()
        else:
            bip44_mst_ctx = Bip44.FromSeed(seed_bytes, self.coin_type)
           
            bip44_acc_ctx = bip44_mst_ctx.Purpose().Coin().Account(0)
            bip44_chg_ctx = bip44_acc_ctx.Change(Bip44Changes.CHAIN_EXT) # if you use "Solflare", remove this line and make a simple code modify and test
            priv_key_bytes = bip44_chg_ctx.PrivateKey().Raw().ToBytes()
            public_key_bytes = bip44_chg_ctx.PublicKey().RawCompressed().ToBytes()[1:]
            key_pair = priv_key_bytes+public_key_bytes

            return bip44_chg_ctx.PublicKey().ToAddress(), base58.Base58Encoder.Encode(key_pair)
```

## test
```
mnemonic = 'oblige receive elite random advance payment wife detect tomorrow source borrow mixture'
print(f'mnemonic: {mnemonic}')
coin_types = {
    Bip44Coins.ETHEREUM: 'ethereum(evm)',
    Bip44Coins.SOLANA: 'solana',
    # Bip44Coins.TERRA: 'luna',
    # Bip44Coins.DASH: 'dash',
    # .....
    # also support other chain, such as file coin, eth classic, doge, dash, luna ....
    # example change coin_type as Bip44Coins.EOS, Bip44Coins.TERRA .....
   
}
for coin_type in coin_types.keys():
    chain_name = coin_types[coin_type]
    bca = BlockChainAccount(mnemonic=mnemonic, coin_type=coin_type)
    address, pk = bca.get_address_pk()
    print(f'{chain_name} mainnet address: {address}, private key: {pk}')
```
## result

```
mnemonic: oblige receive elite random advance payment wife detect tomorrow source borrow mixture
ethereum(evm) mainnet address: 0x58eE4fd2e1D2c970E1fAA8f888CFd1cA27BD4A28, private key: 4a68dfa8cb029fb5490cb36bb9c4c6523bada89134e40d2498cf83d7b4295cfb
solana mainnet address: 6RVympP2ZLR3T3KTSiqzCcBTvjRhT4UDCCt8AsbkYg2b, private key: 58hvcp5Lje9us9Q1QJdWV8aATcJKeHNZudyN9jWuiNYtRGFhJrH97Qe4ew8VmxLx5VCEYEuHGWRZuaFLr6A4euqR
```

## init account with python

```
pip install solana, web3
```

## test solana, eth

```
from solders.keypair import Keypair
from web3 import Web3
# solana
solana_private_key = '58hvcp5Lje9us9Q1QJdWV8aATcJKeHNZudyN9jWuiNYtRGFhJrH97Qe4ew8VmxLx5VCEYEuHGWRZuaFLr6A4euqR'
kp = Keypair().from_base58_string(solana_private_key) # private key
print(kp.pubkey()) # get "6RVympP2ZLR3T3KTSiqzCcBTvjRhT4UDCCt8AsbkYg2b", solana address
# eth
w3 = Web3(Web3.HTTPProvider("https://cloudflare-eth.com/v1/mainnet"))
eth_private_key = '4a68dfa8cb029fb5490cb36bb9c4c6523bada89134e40d2498cf83d7b4295cfb'
ac = w3.eth.account.from_key(eth_private_key) # private_key
print(ac.address) # get "0x58eE4fd2e1D2c970E1fAA8f888CFd1cA27BD4A28", eth address
```

## import the mnemonic seed to phantom wallet , get result
![phantom](https://github.com/satisfywithmylife/phantom-py/assets/30144807/5eecbe32-3c6a-4b60-9cc1-504b8dc8b413)

# last but important!
1. test the result and compare it with main web wallet app(such as: metamask, mathwallet, trustwallet...) before you deposit crypto assets to the address
2. some wallet may get diffrent result, because it may use diffrent derive path to generate wallet
3. learn about hd-wallet principle by your self

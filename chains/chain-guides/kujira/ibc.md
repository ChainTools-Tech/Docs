---
description: >-
  Here is a list of wasm contract code instructions that will enable you to swap
  using the CLI.
---

# IBC



<pre class="language-javascript"><code class="lang-javascript"><strong>///Get the correct IBC asset to show
</strong><strong>
</strong><strong>kujirad query ibc-transfer denom-trace &#x3C;IBC-DENOM-HERE> 
</strong>
///This will result in showing you what the ticker is on the IBC hash. 

kujirad query ibc-transfer denom-trace 004EBF085BBED1029326D56BE8A2E67C08CECE670A94AC1947DF413EF5130EB2 

///will show 

denom_trace: base_denom: wavax-wei path: transfer/channel-9
</code></pre>



Listed assets on Kujira.&#x20;

| ASSET      | IBC CODE                                                         | IBC-CHANNEL   |
| ---------- | ---------------------------------------------------------------- | ------------- |
| avax-wei   | 004EBF085BBED1029326D56BE8A2E67C08CECE670A94AC1947DF413EF5130EB2 | channel-9     |
| uusdc      | 05554A9BFDD28894D7F18F4C707AA0930D778751A437A9FE1F4684A3E1199728 | channel-3/208 |
| weth-wei   | B38805B1C75352B28169284F96DF56BDEBD9E8FAC005BDCC8CF0378C82AA8E7  | channel-9     |
| uatom      | 27394FB092D2ECCD56123C74F36E4C1F926001CEADA9CA97EA622B25F41E5EB2 | channel-0     |
| axlr-uusdc | 295548A78785A1007F232DE286149A6FF512F180AF5657780FC89C009E2C348F | channel-9     |
| uosmo      | 47BD209179859CDE4A2806763D7189B6E6FE13A17880FE2B42DE1E6C1E329E23 | channel-3     |
| ustars     | 4F393C3FCA4190C0A6756CE7F6D897D5D1BE57D6CCB80D0BC87393566A7B6602 | channel-7     |
| uscrt      | A358D7F19237777AF6D8AD0E0F53268F8B18AE8A53ED318095C14D6D7F3B2DB5 | channel-10    |
| uluna      | DA59C009A0B3B95E0549E6BF7B075C8239285989FF457A8EDDBB56F10B2A6986 | channel-5     |
| ujuno      | EFF323CC632EC4F747C61BCE238A758EFDB7699C3226565F7C20DA06509D59A5 | channel-1     |
| aevmos     | F3AA7EF362EC5E791FE78A0F4CCC69FEE1F9A7485EB1A8CAB3F6601C00522F10 | channel-23    |

Query  bank balance of  wallet

```javascript


kujirad q bank balances kujiara123456789 --node https://rpc.kujira.chaintools.tech:443

balances:
- amount: "3141373901494"
  denom: ibc/004EBF085BBED1029326D56BE8A2E67C08CECE670A94AC1947DF413EF5130EB2
- amount: "1753813"
  denom: ibc/05554A9BFDD28894D7F18F4C707AA0930D778751A437A9FE1F4684A3E1199728
- amount: "18408101934674"
  denom: ibc/1B38805B1C75352B28169284F96DF56BDEBD9E8FAC005BDCC8CF0378C82AA8E7
- amount: "244381"
  denom: ibc/27394FB092D2ECCD56123C74F36E4C1F926001CEADA9CA97EA622B25F41E5EB2
- amount: "48466"
  denom: ibc/47BD209179859CDE4A2806763D7189B6E6FE13A17880FE2B42DE1E6C1E329E23
- amount: "17190"
  denom: ibc/4F393C3FCA4190C0A6756CE7F6D897D5D1BE57D6CCB80D0BC87393566A7B6602
- amount: "13636"
  denom: ibc/A358D7F19237777AF6D8AD0E0F53268F8B18AE8A53ED318095C14D6D7F3B2DB5
- amount: "16332"
  denom: ibc/DA59C009A0B3B95E0549E6BF7B075C8239285989FF457A8EDDBB56F10B2A6986
- amount: "19672"
  denom: ibc/EFF323CC632EC4F747C61BCE238A758EFDB7699C3226565F7C20DA06509D59A5
- amount: "3700092214505742"
  denom: ibc/F3AA7EF362EC5E791FE78A0F4CCC69FEE1F9A7485EB1A8CAB3F6601C00522F10
- amount: "14809184"
  denom: ukuji
pagination:
  next_key: null
  total: "0"

```

This example will swap 7 `axlr-usdc` to `kuji` at market price &#x20;

```javascript
kujirad tx wasm execute kujira14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9sl4e867 
 '{"swap": {}}' --amount 7000000ibc/295548A78785A1007F232DE286149A6FF512F180AF5657780FC89C009E2C348F 
  --from Highlander-Main 
   --gas auto --gas-adjustment 1.2 
    --fees 1800ukuji 
     --chain-id kaiyo-1  
      --node https://rpc.kujira.chaintools.tech:443
```

<figure><img src="../../../.gitbook/assets/Screenshot 2022-08-22 at 18.35.48.png" alt=""><figcaption><p>Take a look at the contract address in the address bar on the page. Matches the same as the CLI </p></figcaption></figure>

This example with swap 1 `atom` to `kuji` at market order

```javascript
kujirad tx wasm execute kujira18v47nqmhvejx3vc498pantg8vr435xa0rt6x0m6kzhp6yuqmcp8s4x8j2c  
 '{"swap": {}}' --amount 1000000ibc/27394FB092D2ECCD56123C74F36E4C1F926001CEADA9CA97EA622B25F41E5EB2  
  --from Highlander-Main 
   --gas auto 
    --gas-adjustment 1.2 
     --fees 1800ukuji 
      --chain-id kaiyo-1  
       --node https://rpc.kujira.chaintools.tech:443
```


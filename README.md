# ETHstaking

Create ETH validators using the Coinbase Cloud APIs

Use Coinbase Cloud UI to spin up a Cluster: [here](https://console.cloud.coinbase.com/participate)

This can also be accomplished programatically

First, generate an API key in the UI: [here](https://console.cloud.coinbase.com/api-token-management)

Collect your wallet address and private key

```
import requests

baseUrl = "https://api.coinbasecloud.net/eth2/v1/"
apiToken = "XGXD...WV"
walletAddr = "0x01...f09"
headers = {
    "Accept": "application/json",
    "Content-Type": "application/json",
    "apikey": apiToken
}

payload = {
    "name": "demo_cluster",
    "network": "mainet",
    "region": "us-east-2.aws",
    "client": "lighthouse",
    "remoteSigner": False
}

response = requests.post(baseUrl+"clusters", json=payload, headers=headers)
```

GET ResourceID for Clusters

```
response = requests.request("GET", baseUrl+"clusters", headers=headers)

body=response.json()

clusterID=body['clusters'][0]['resourceID']
```

Create your Validators (1 to 334) in the Cluster

```
payload = {
    "count": 1,
    "resultType": "depositData",
    "targetResource": clusterID,
    "eth1WithdrawalAddress": "{{eth1WalletAddress}}"
}

response = requests.post(baseUrl+"validators", json=payload, headers=headers)

body=response.json()

allocationID=body['allocationID']
```

Run GET Allocation to return your DepositData body

```
response = requests.get(baseUrl+"allocations/"+allocationID, headers=headers)

body=response.json()

//the raw hash to submit to the staking contract
rawHash=body['depositData'][0]['raw']

//formatted body to use with ethereal
formattedBody=body['depositData'][0]['formatted']

f = open("deposit-data-formatted.json", "a")
f.write(formattedBody)
f.close()
//save this to file
```

Use ethereal utility to submit DepositData https://github.com/wealdtech/ethereal

```
DEPOSITDATA=$(cat ./deposit-data-formatted.json)

ethereal beacon deposit \
      --network=goerli \
      --data="$DEPOSITDATA" \
      --privatekey=$WALLETPRIVKEY \
      --from=$WALLETADDR \
      --value="32 Ether"
```

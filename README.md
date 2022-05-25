# ETHstaking
Create ETH validators using the Coinbase Cloud APIs


//Use Coinbase Cloud UI to spin up a Cluster:  app.coinbase.co/particpate This can also be accomplished programatically

import requests

url = "https://api.bisontrails.co/eth2/v1/clusters"

payload = {
    "name": "demo_cluster",
    "network": "mainet",
    "region": "us-east-2.aws",
    "client": "lighthouse",
    "remoteSigner": False
}
headers = {
    "Accept": "application/json",
    "Content-Type": "application/json"
}

response = requests.post(url, json=payload, headers=headers)

print(response.text)

//Generate API key in the UI: app.coinbase.co/api-token-management
//Have to see if this can be done programatically


//GET ResourceID for Clusters


import requests

url = "https://api.bisontrails.co/eth2/v1/clusters"

payload={}
headers = {
  'apikey': '{{token}}'
}

response = requests.request("GET", url, headers=headers, data=payload)

print(response.text)


//Create your Validators (1 to 334) in the Cluster using this POST

import requests

url = "https://api.bisontrails.co/eth2/v1/validators"

payload = {
    "count": 1,
    "resultType": "depositData",
    "targetResource": "{{ResourceID}}",
    "eth1WithdrawalAddress": "{{eth1WalletAddress}}"
}
headers = {
    "Accept": "application/json",
    "Content-Type": "application/json"
}

response = requests.post(url, json=payload, headers=headers)

print(response.text)

//Returns {{AllocationID}}

//Run GET Allocation to return your DepositData body
  
import requests

url = "https://api.bisontrails.co/eth2/v1/allocations/%7B%7BAllocationID%7D%7D"

headers = {"Accept": "application/json"}

response = requests.get(url, headers=headers)

print(response.text)

//Use ethereal utility to submit DepositData https://github.com/wealdtech/ethereal

# API Documentation

## Lookup users

#### anchor_tag:
lookup_users

#### description:
Looks up the data for one or more users by their usernames. In order to perform more than one lookup at once, include a set of comma-separated usernames in the URL in place of the single username.

#### response_description:
Returns an object with a top-level key for each username looked up. Each top-level key contains an sub-object that has a "profile" field and a "verifications" field.

#### method:
GET

#### path_template:
/users/{usernames}

#### tryit_pathname:
/v1/users/fredwilson?app-id=demo-app-id&app-secret=demo-app-secret

#### example_request_bash:
curl https://api.onename.com/v1/users/fredwilson \
    -u 'YOUR-API-ID:YOUR-API-SECRET'

#### example_response:
{
  "fredwilson": {
    "profile": {
      "avatar": {
        "url": "https://s3.amazonaws.com/kd4/fredwilson1"
      },
      "bio": "I am a VC",
      "bitcoin": {
        "address": "1Fbi3WDPEK6FxKppCXReCPFTgr9KhWhNB7"
      },
      "cover": {
        "url": "https://s3.amazonaws.com/dx3/fredwilson"
      },
      "facebook": {
        "proof": {
          "url": "https://facebook.com/fred.wilson.963871/posts/10100401430876108"
        },
        "username": "fred.wilson.963871"
      },
      "graph": {
        "url": "https://s3.amazonaws.com/grph/fredwilson"
      },
      "location": {
        "formatted": "New York City"
      },
      "name": {
        "formatted": "Fred Wilson"
      },
      "twitter": {
        "proof": {
          "url": "https://twitter.com/fredwilson/status/533040726146162689"
        },
        "username": "fredwilson"
      },
      "v": "0.2",
      "website": "http://avc.com"
    },
    "verifications": [
      {
        "identifier": "fredwilson",
        "proof_url": "https://twitter.com/fredwilson/status/533040726146162689",
        "service": "twitter",
        "valid": true
      },
      {
        "identifier": "fred.wilson.963871",
        "proof_url": "https://facebook.com/fred.wilson.963871/posts/10100401430876108",
        "service": "facebook",
        "valid": true
      }
    ]
  }
}

_end_

## Search users

#### anchor_tag:
search_users

#### description:
Takes in a search query and returns a list of results that match the search. 
The query is matched against +usernames, full names, and twitter handles by default.

It's also possible to explicitly search verified Twitter, Facebook, Github 
accounts, and verified domains. This can be done by using search queries like 
twitter:albertwenger, facebook:g3lepage, github:shea256, domain:muneebali.com


#### response_description:
Returns an array of results, where each result has a \"profile\" object.

#### method:
GET

#### path_template:
/search

#### parameters[]:
{"name": "query", "description": "The text to search for."}

#### tryit_pathname:
/v1/search?query=wenger&app-id=demo-1234&app-secret=demo-1234

#### example_request_bash:
curl https://api.onename.com/v1/search?query=wenger \
    -u 'YOUR-API-ID:YOUR-API-SECRET'

#### example_response:
{
  "results": [
    {
      "profile": {
        "avatar": {
          "url": "https://pbs.twimg.com/profile_images/1773890030/aew_artistic_bigger.gif"
        },
        "bio": "VC at USV.com",
        "bitcoin": {
          "address": "1QHDGGLEKK7FZWsBEL78acV9edGCTarqXt"
        },
        "cover": {
          "url": "https://s3.amazonaws.com/dx3/albertwenger"
        },
        "facebook": {
          "proof": {
            "url": "https://www.facebook.com/albertwenger/posts/10152554952070219"
          },
          "username": "albertwenger"
        },
        "github": {
          "proof": {
            "url": "https://gist.github.com/albertwenger/03c1b5db3880998115fa"
          },
          "username": "albertwenger"
        },
        "graph": {
          "url": "https://s3.amazonaws.com/grph/albertwenger"
        },
        "location": {
          "formatted": "New York"
        },
        "name": {
          "formatted": "Albert Wenger"
        },
        "twitter": {
          "proof": {
            "url": "https://twitter.com/albertwenger/status/499594071401197568"
          },
          "username": "albertwenger"
        },
        "v": "0.2",
        "website": "http://continuations.com"
      },
      "username": "albertwenger"
    }
  ]
}

_end_

## Register users

#### anchor_tag:
register_users

#### description: 
Registers a new blockchain ID and transfers the ownership to
a bitcoin address. Takes in a username to be registered along with the address
that will own the blockchain ID. Optionally, takes in the profile data that should
be associated with the blockchain ID being registered. Returns a status object that
shows if the request was successfully received. It takes on the order of hours
to actually complete the registration.

#### parameters[]:
{"name": "username", "description": "The username (blockchain ID username) that is to be registered."}
{"name": "recipient_address", "description": "The bitcoin address that the blockchain ID will be transferred to once it has been registered."}
{"name": "profile", "description": "The data to be associated with the blockchain ID.", "optional": true}

#### response_description:
Returns an object with a status that is either "success" or "error".

#### method:
POST

#### path_template:
/users

#### example_request_bash:
curl https://api.onename.com/v1/users \
    -u 'YOUR-API-ID:YOUR-API-SECRET' \
    -d '{"username": "fredwilson",
         "recipient_address": "152f1muMCNa7goXYhYAQC61hxEgGacmncB",
         "profile": {"bio": "I am a VC"}}' \
    -H 'Content-type: application/json' \
    -X POST

#### example_response:
{
    "status": "success"
}

_end_

## Update users

#### anchor_tag:
update_users

#### description: 
Update a blockchain ID profile on the blockchain. For a given username, takes in
the new profile data and public key of the bitcoin owner address. Returns an unsigned
transaction that needs to be signed client side and broadcasted using the
transaction broadcast endpoint. The unsigned transaction already contains the
signed transaction fee and only name update input needs to be signed. It takes
on the order of hours to update the profile data on the blockchain.

#### parameters[]:
{"name": "profile", "description": "JSON profile data that should be associated with the username."}
{"name": "owner_pubkey", "description": "Public key of the bitcoin address that owns the username."}

#### response_description:
Returns an object with an unsigned transaction "unsigned_tx" in hex format.

#### method:
POST

#### path_template:
/users/{username}/update

#### example_request_bash:
curl https://api.onename.com/v1/users/fredwilson/update \
    -u 'YOUR-API-ID:YOUR-API-SECRET' \
    -d '{"profile": {"bio": "I am a VC"}, 
         "owner_pubkey": "02b262e2bdb4fee2834115aab77..."}' \
    -H 'Content-type: application/json' \
    -X POST

#### example_response:
{
    "unsigned_tx": "01000000027757f96d886019cf8307e3b3c35bee845...."
}

_end_

## Transfer users

#### anchor_tag:
transfer_users

#### description: 
Transfer the ownership of a blockchain ID to a new bitcoin address. For a given username, takes in
the new address that should own the blockchain ID and public key of the current bitcoin owner address.
Returns an unsigned transaction that needs to be signed client side and broadcasted using the
transaction broadcast endpoint. The unsigned transaction already contains the
signed transaction fee and only name transfer input needs to be signed. It takes
on the order of hours to transfer the blockchain ID on the blockchain.

#### parameters[]:
{"name": "transfer_address", "description": "Bitcoin address of the new owner address."}
{"name": "owner_pubkey", "description": "Public key of the bitcoin address that currently owns the username."}

#### response_description:
Returns an object with an unsigned transaction "unsigned_tx" in hex format.

#### method:
POST

#### path_template:
/users/{username}/transfer

#### example_request_bash:
curl https://api.onename.com/v1/users/fredwilson/transfer \
    -u 'YOUR-API-ID:YOUR-API-SECRET' \
    -d '{"transfer_address": "19bXfGsGEXewR6TyAV3b89cSHBtFFewXt6", 
         "owner_pubkey": "02b262e2bdb4fee2834115aab77..."}' \
    -H 'Content-type: application/json' \
    -X POST

#### example_response:
{
    "unsigned_tx": "01000000027757f96d886019cf8307e3b3c35bee845...."
}

_end_

## Get all users

#### anchor_tag:
get_userbase

#### description:
Gets all data for the decentralized namespace, including the total number of users registered.

#### response_description:
Returns an object with "stats", and "usernames". "stats" is a sub-object which in turn contains a "registrations" field that reflects a running count of the total users registered. "usernames" is a list of all usernames in the namespace.

#### tryit_pathname:
/v1/users

#### method:
GET

#### path_template:
/users

#### example_request_bash:
curl https://api.onename.com/v1/users \
    -u 'YOUR-API-ID:YOUR-API-SECRET'

#### example_response:
{
  "stats": {
    "registrations": "37000"
  },
  "usernames": [
    "fredwilson",
    ...
  ]
}

_end_

## Broadcast transactions

#### anchor_tag:
broadcast_transaction

#### description:
Takes in a signed transaction (in hex format) and broadcasts it to the network. If the transaction is successfully broadcasted, the transaction hash is returned in the response.

#### response_description:
Returns an object with a status that is either "success" or "error".

#### method:
POST

#### path_template:
/transactions

#### parameters[]:
{"name": "signed_hex", "description": "A signed transaction in hex format."}

#### example_request_bash:
curl https://api.onename.com/v1/transactions \
    -u 'YOUR-API-ID:YOUR-API-SECRET' \
    -d '{"signed_hex": "00710000015e98119922f0b"}' \
    -H 'Content-Type: application/json' \
    -X POST

#### example_response:
{
    "status": "success"
}

_end_

## Get unspent outputs

#### anchor_tag:
unspent_outputs

#### description:
Retrieves the unspent outputs for a given address so they can be used for building transactions.

#### response_description:
Returns an array of unspent outputs for a provided address.

#### method:
GET

#### path_template:
/addresses/{address}/unspents

#### tryit_pathname:
/v1/addresses/19bXfGsGEXewR6TyAV3b89cSHBtFFewXt6/unspents?app-id=demo-1234&app-secret=demo-1234

#### example_request_bash:
curl https://api.onename.com/v1/addresses/19bXfGsGEXewR6TyAV3b89cSHBtFFewXt6/unspents \
    -u 'YOUR-API-ID:YOUR-API-SECRET'

#### example_response:
{
  "unspents": [
    {
      "confirmations": 6715, 
      "output_index": 1, 
      "script_hex": "76a9145e48be183fbb5c3990e29aedd3b44367c28a5e4388ac", 
      "transaction_hash": "97a2dc2270be32f322c4e9fdf4de9754136a0c9a83abe0d655a6edc19cf01e15", 
      "value": 5500
    }, 
    {
      "confirmations": 7523, 
      "output_index": 1, 
      "script_hex": "76a9145e48be183fbb5c3990e29aedd3b44367c28a5e4388ac", 
      "transaction_hash": "d4d5a583a229409af3c9c29e8f9cb5b40b8c655509307ad0396a0d38fd7df906", 
      "value": 5500
    }
  ]
}

_end_

## Get names owned

#### anchor_tag:
names_owned

#### description:
Retrieves a list of names owned by the address provided.

#### response_description:
Returns an array of the names that the address owns.

#### method:
GET

#### path_template:
/addresses/{address}/names

#### tryit_pathname:
/v1/addresses/1QJQxDas5JhdiXhEbNS14iNjr8auFT96GP/names?app-id=demo-1234&app-secret=demo-1234

#### example_request_bash:
curl https://api.onename.com/v1/addresses/1QJQxDas5JhdiXhEbNS14iNjr8auFT96GP/names \
    -u 'YOUR-API-ID:YOUR-API-SECRET'

#### example_response:
{
  "names": [
    "muneeb.id"
  ]
}

_end_

## Get DKIM public key

#### anchor_tag:
dkim_pubkey

#### description:
Retrieves a DKIM public key for given domain, using the "blockchainid._domainkey" subdomain DNS record.

#### response_description:
Returns a DKIM public key.

#### method:
GET

#### path_template:
/domains/{domain}/dkim

#### tryit_pathname:
/v1/domains/onename.com/dkim?app-id=demo-1234&app-secret=demo-1234

#### example_request_bash:
curl https://api.onename.com/v1/domains/onename.com/dkim \
    -u 'YOUR-API-ID:YOUR-API-SECRET'

#### example_response:
{
  "key_curve": "secp256k1", 
  "key_type": "ecdsa", 
  "public_key": "MFYwEAYHKoZIzj0CAQYFK4EEAAoDQgAE3s0qMljBNrC6mmfMLQp1B+v8haqeOswDV4r0p6HogXA7JT0bOt4nIom6IQeE+TQRzdn4fz+VWBxIBkL9nAXIRQ=="
}

_end_

## Get user stats

#### anchor_tag:
user_stats

#### description:
Gets all user stats.

#### response_description:
Returns an object with "stats".

#### method:
GET

#### path_template:
/stats/users

#### tryit_pathname:
/v1/stats/users?app-id=demo-1234&app-secret=demo-1234

#### example_request_bash:
curl https://api.onename.com/v1/stats/users \
    -u 'YOUR-API-ID:YOUR-API-SECRET'

#### example_response:
{
  "stats": {
    "registrations": "31804"
  }
}

_end_
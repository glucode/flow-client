[![Gem Version](https://badge.fury.io/rb/flow_client.svg)](https://badge.fury.io/rb/flow_client)
![Rubocop Analysis](https://github.com/glucode/flow_client/actions/workflows/rubocop-analysis.yml/badge.svg)
![RSpec Tests](https://github.com/glucode/flow_client/actions/workflows/ruby.yml/badge.svg)

# FlowClient

FlowClient is a Ruby gRPC client for Flow (onflow.org)

#### Features

Blocks:
- [ ] retrieve a block by ID
- [x] retrieve a block by height
- [x] retrieve the latest block

Collections:
- [ ] retrieve a collection by ID 

Events:
- [x] retrieve events by name in the block height range

Scripts:
- [x] submit a script and parse the response
- [x] submit a script with arguments
- [x] create a script that returns complex structure and parse the response

Accounts:
- [x] retrieve an account by address
- [x] create a new account
- [ ] deploy a new contract to the account
- [ ] remove a contract from the account
- [ ] update an existing contract on the account

Transactions: 
- [x] retrieve a transaction by ID
- [x] sign a transaction with same payer, proposer and authorizer
- [x] sign a transaction with different payer and proposer
- [x] sign a transaction with different authorizers using sign method multiple times
- [x] submit a signed transaction
- [x] sign a transaction with arguments and submit it


## Installation

Add this line to your application's Gemfile:

```ruby
gem 'flow_client'
```

And then execute:

    $ bundle install

Or install it yourself as:

    $ gem install flow_client

## Usage

```ruby
# Connect to the Flow testnet
client = FlowClient::Client.new("access.devnet.nodes.onflow.org:9000")
res = client.ping
```
### Accounts

```ruby
# get_account(account_address)
res = client.get_account("0xAlice")
```

### Events

```ruby
# get_events(type, start_height, end_height)
res = client.get_events("A.0b2a3299cc857e29.TopShot.Deposit", 12913388, 12913389)
```

### Scripts

```ruby
# execute_script(cadence_string, args_array)
args = [{ type: "Address", value: "0xAlice" }.to_json]
# Execute a Cadence script
res = client.execute_script(script, args)
```

### Transactions

```ruby
key = FlowClient::Crypto.key_from_hex_keys(
  "<priv key hex>"
)

# Send a transaction with a single signer, proposer and authorizer
transaction = FlowClient::Transaction.new
transaction.script = cadence
transaction.reference_block_id = ref_block_id
transaction.proposer_address = "0xAlice"
transaction.proposer_key_index = 0
transaction.arguments = [
  { type: "Address", value: user_address }.to_json
]
transaction.proposer_key_sequence_number = sequence_number
transaction.payer_address = "0xAlice"
transaction.authorizer_addresses = ["0xAlice"]
transaction.add_envelope_signature("0xAlice", 0, key)
res = client.send_transaction(transaction)

# Get a transaction
# get_transaction(transaction_id)
client.get_transaction(res.id.unpack("H*"))

# Get a transaction result
# get_transaction_result(transaction_id)
client.get_transaction_result(res.id.unpack("H*"))
```

### Address Alias Resolution

Using address aliases is handy for switching between different environments.

```ruby
cadence =  %{
    import FungibleToken from 0xFungibleToken
    
    ...
}

client.address_aliases = { "0xFungibleToken": "0x123234545" }

# 0xFungibleToken get resolved to the address "0x123234545"
res = client.execute_script(cadence)
```

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake spec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and the created tag, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/glucode/flow_client. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [code of conduct](https://github.com/[USERNAME]/flow_client/blob/master/CODE_OF_CONDUCT.md).

## License

The gem is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).

## Code of Conduct

Everyone interacting in the FlowClient project's codebases, issue trackers, chat rooms and mailing lists is expected to follow the [code of conduct](https://github.com/[USERNAME]/flow_client/blob/master/CODE_OF_CONDUCT.md).

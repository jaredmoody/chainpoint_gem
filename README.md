# Chainpoint

[![Build Status](https://travis-ci.org/KenjiOhtsuka/chainpoint_gem.svg?branch=master)](https://travis-ci.org/KenjiOhtsuka/chainpoint_gem)

[![Gem Version](https://badge.fury.io/rb/chainpoint.svg)](https://badge.fury.io/rb/chainpoint)

[Rubygem Page](https://rubygems.org/gems/chainpoint)

[API Document](https://kenjiohtsuka.github.io/chainpoint_gem/)

This is gem for request to Tierion Chainpoint.
For Chainpoint, look at [Chainpoint Node HTTP API](https://github.com/chainpoint/chainpoint-node/wiki/Node-HTTP-API).

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'chainpoint'
```

And then execute:

    $ bundle


Or install it yourself as:

    $ gem install chainpoint

## Usage

### Create Instance

```ruby
require 'chainpoint'
chainpoint = Chainpoint.new
```

### Submit Hash

Use this function to submit a hashes, and receive back the information needed to later retrieve a proof using `get_proof`

By default hashes are submitted to three Nodes to help ensure a proof will become available at the appropriate time. Only one such proof need be permanently stored, the others provide redundancy.

```ruby
# hash is SHA256
# hash = '2fbe59be2be10a4fdeca9c6d3e9f56fc56fb3ee9a8ef2e9be37fced60c264681'
chainpoint.submit(hash)
```

#### Response

```ruby
[
  {
    "hash_id_node" => "1d1aa8a0-8f42-11e8-967f-01a68adfc010",
    "hash" => "2fbe59be2be10a4fdeca9c6d3e9f56fc56fb3ee9a8ef2e9be37fced60c264681"
    "uri" => "http://45.77.197.76"
  }
]

```

Or, use `submit_data`:

```ruby
chainpoint.submit_data("text")
```

### Get Proof

Only the node that a hash was submitted to can retrieve the proof, so initialize the client with
the uri from the submit response and pass the hash_id_node from the same response:

```ruby
chainpoint = Chainpoint.new('http://45.77.197.76')
chainpoint.get_proof(hash_id_node)
```

#### Response

```
[
  {
    "hash_id_node"=>"a50eb4d0-8f20-11e8-8da8-0133565a0e60",
    "proof"=>"eJyNVMGOHDUQ5SP4BI7MTpXLLtt9Wolf4JTLyC6XmZaWmdF0JyHHhAtH9hMgizYgLkgoR/5jJD6G6tlN0O6ClEN3q22/V69cr+qHd5ey38363fzXdp4P07Bev6SxXeyP36xlW8bdYT/u5vULuplfHfS3rz4u3WzLtD1dul415KquKkLxvamULNxIcw/cxZ5Kqrkk7U7tIMUu2hjEseeEvy80m7Ftdvumpy9KAK2+wSp1BytETavUSloBEgUOBZTh/RkyPa/fjvOsd8hNmf90gHYwrpz/GvIAYQB49pFe9seFPoEXCg/oMxSjl9icBldLeEy/IP+bPjx7V49lJ1udrt/8clWqXv0h5WqzLO2Pm7u9t/vD9Pdnn7/+6er05Vnp2IZPyfL1z/vD7bQtKxf4DD7rWMCfkMNj8I+7cZoHDOS8I/AwtBijbx08Bo1aNUPKDTxV7uQoOQ6JtFLqQcEqlynVXtmD1wa5umhlpViJXER2JTTA0JisotC8OKeVMVgmkaVnAUQObCCreQJK9nos8NJ7xS4ZmIu65ok8lALdfNMFU0mYkodmvkKq3jF5VukSu0fhQFEeEh5Pl4196GjZmi89InaS1LnZr7rQagWDmRhkAo5UpMZumwAVs4+a6hPCnDGbex1LK8Bm94TYcs/dW7ZCuWVEqAGc3S9HbdW4oPvlE13P/ISwphyyL+yT3Wlk5hwqhhTsvq0zavApaFMO6HO09J1a7fpC3RGaRH1CyLFbGTwZh6+lcGzeBIWUk5fMORfruu5IQgZXoZnPzNN9KfkSMj5xTbYUncvxg3GsjAMOH8aEXPw7H5ZxMZj1h3vEWY5YEPOzqki3wKU5cz0hSEiNkwjXDsRm/CKliSegiFhRTf9isPJAzu1dU03X358H0VsL9ut9n43t9j7szfPjOF2fLv5P4tpQumvluL4HrJde/wcFK5lW",
    "anchors_complete"=>["cal"]
  }
]
```

### Decode Proof

A proof string can be converted to JSON to view the Chainpoint JSON Schema
```ruby
chainpoint.decode_proof(proof)
```

### Verify

Pass proof value which is shown on calling `get_proof`.

```ruby
chainpoint.verify('eJyNVMGOHDUQ5SP4BI7MTpXLLtt9Wolf4JTLyC6XmZaWmdF0JyHHhAtH9hMgizYgLkgoR/5jJD6G6tlN0O6ClEN3q22/V69cr+qHd5ey38363fzXdp4P07Bev6SxXeyP36xlW8bdYT/u5vULuplfHfS3rz4u3WzLtD1dul415KquKkLxvamULNxIcw/cxZ5Kqrkk7U7tIMUu2hjEseeEvy80m7Ftdvumpy9KAK2+wSp1BytETavUSloBEgUOBZTh/RkyPa/fjvOsd8hNmf90gHYwrpz/GvIAYQB49pFe9seFPoEXCg/oMxSjl9icBldLeEy/IP+bPjx7V49lJ1udrt/8clWqXv0h5WqzLO2Pm7u9t/vD9Pdnn7/+6er05Vnp2IZPyfL1z/vD7bQtKxf4DD7rWMCfkMNj8I+7cZoHDOS8I/AwtBijbx08Bo1aNUPKDTxV7uQoOQ6JtFLqQcEqlynVXtmD1wa5umhlpViJXER2JTTA0JisotC8OKeVMVgmkaVnAUQObCCreQJK9nos8NJ7xS4ZmIu65ok8lALdfNMFU0mYkodmvkKq3jF5VukSu0fhQFEeEh5Pl4196GjZmi89InaS1LnZr7rQagWDmRhkAo5UpMZumwAVs4+a6hPCnDGbex1LK8Bm94TYcs/dW7ZCuWVEqAGc3S9HbdW4oPvlE13P/ISwphyyL+yT3Wlk5hwqhhTsvq0zavApaFMO6HO09J1a7fpC3RGaRH1CyLFbGTwZh6+lcGzeBIWUk5fMORfruu5IQgZXoZnPzNN9KfkSMj5xTbYUncvxg3GsjAMOH8aEXPw7H5ZxMZj1h3vEWY5YEPOzqki3wKU5cz0hSEiNkwjXDsRm/CKliSegiFhRTf9isPJAzu1dU03X358H0VsL9ut9n43t9j7szfPjOF2fLv5P4tpQumvluL4HrJde/wcFK5lW')
```

#### Response

```ruby
[
  {
    "proof_index"=>0,
    "hash"=>"2fbe59be2be10a4fdeca9c6d3e9f56fc56fb3ee9a8ef2e9be37fced60c264681",
    "hash_id_node"=>"a50eb4d0-8f20-11e8-8da8-0133565a0e60",
    "hash_submitted_node_at"=>"2018-07-24T09:05:00Z",
    "hash_id_core"=>"a804c350-8f20-11e8-890a-01c7d2e52ba5",
    "hash_submitted_core_at"=>"2018-07-24T09:05:05Z",
    "anchors"=>[
      {
        "branch"=>"cal_anchor_branch",
        "type"=>"cal",
        "valid"=>true
      }
    ],
    "status"=>"verified"
  }
]
```

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake spec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on [GitHub chainpoint](https://github.com/KenjiOhtsuka/chainpoint_gem).

## Other

* PHP [Composer Package](https://packagist.org/packages/kenji-otsuka/chainpoint) and its [GitHub Repository](https://github.com/KenjiOhtsuka/chainpoint_php).

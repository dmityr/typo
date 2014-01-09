# GossipGirl

GossipGirl provides easy way for distributed Ruby application to organise clusters and exchange state and data between different parts of cluster.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'gossip_girl'
```

And then execute:

```bash
$ bundle
```

Or install it yourself as:

```bash
$ gem install gossip_girl
```

## Usage

Gem allow to exchange state and data between nodes.  
HeartBeat - allow to exchange states.  
DataExchange - allow to exchange data.

#### HeartBeat Configuration

Example of configuration for HeartBeat:

```ruby
{
    cluster_name: 'TestCluster',
    address: "tcp://127.0.0.1:4000",
    failure_threshold: 8,
    beat_interval: 1.2,
    seeds: {"tcp://127.0.0.1:4001"=>{}, "tcp://127.0.0.1:4002"=>{}}
}
```

#### HeartBeat Usage

Example of HeartBeat Usage:

```ruby
configuration_hash = {
    cluster_name: 'TestCluster',
    address: "tcp://127.0.0.1:4000,
    failure_threshold: 8,
    beat_interval: 1.2,
    seeds: {"tcp://127.0.0.1:4001"=>{}, "tcp://127.0.0.1:4002"=>{}}
}

heartbeat = HeartBeat.new configuration_hash
f_running = true

trap 'INT' do
  f_running = false
end

while f_running do

  heartbeat.beat

  puts "live_nodes: #{heartbeat.live_nodes}"
  puts "dead_nodes: #{heartbeat.dead_nodes}"

  sleep 1.5
end

heartbeat.close
```

At first we define **configuration_hash** with some data for new HeartBeat instance.
Then in the cycle we check status of all nodes we mentioned like **seeds** in our **configuration_hash**


##### .beat
Send message to each node to know is it still *live* or *dead*?

##### .live_nodes
Fetch live nodes.

##### .dead_nodes
Fetch dead nodes.

#### DataExchange Configuration

DataExchange not require some specific configuration, you just need creat new instance without any params.

#### DataExchange Usage

Example of creation new DataExchange instance:

```ruby
DataExchange.new
```

Example of usage:

```ruby
exchange_file = "*path-to-some-file*"
exchange_server = "*address-of-server-where-we-want-to-send-file*"

d_ex = DataExchange.new
d_ex.bind "tcp://127.0.0.1:7777"

msg = {
    filename: File.basename(exchange_file),
    filesize: File.size(exchange_file),
    data: File.read(exchange_file)
}

d_ex.send_data exchange_server, 'file', msg
```

##### .bind(address)
Binds the socket to an address

##### .send_data(server, request_type, msg)
Send some data by specified address


## Contributing

1. Fork it
2. Create your feature branch (git checkout -b my-new-feature)
3. Commit your changes (git commit -am 'Add some feature')
4. Push to the branch (git push origin my-new-feature)
5. Create new Pull Request

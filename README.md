# nagios-groestlcoin
Nagios plugin to monitor groestlcoind, to allow escalation of alerts when problems are sensed. Can be used on either the GRS or TGRS networks as specified.

## Requirements
- A [Nagios](https://www.nagios.org/) installation
- [jq](https://stedolan.github.io/jq/) for JSON parsing
- groestlcoind with [RPC enabled](https://en.bitcoin.it/wiki/API_reference_(JSON-RPC))

## Setup
### Script
The easiest way of using this script is to check it out directly from Github into your Nagios plugins directory:
```
$ cd /usr/lib/nagios/plugins
$ git clone https://github.com/groestlcoin/nagios-groestlcoin.git groestlcoin
```

### Nagios Commands
Paste the following into the appropriate Nagios command configuration file
```
define command {
        command_line                   $USER1$/groestlcoin/check_groestlcoind.sh -u $ARG1$ -p $ARG2$ -H $HOSTADDRESS$ -P $ARG3$ -B $ARG4$ -w $ARG5$ -c $ARG6$ -t blockchain
        command_name                   check_groestlcoin_blockchain
}

define command {
        command_line                   $USER1$/groestlcoin/check_groestlcoind.sh -u $ARG1$ -p $ARG2$ -H $HOSTADDRESS$ -P $ARG3$ -B $ARG4$ -w $ARG5$ -c $ARG6$ -t connections
        command_name                   check_groestlcoin_connections
}

```

### Nagios Services
Paste the following into your Nagios service configuration file, changing the values as needed
```
define service {
        check_command                  check_groestlcoin_connections!<USERNAME>!<PASSWORD>!<PORT>!<CURRENCY>!<WARN>!<CRIT>
        host_name                      <HOSTNAME>
        service_description            Groestlcoind Connections
        use                            generic-service
}

define service {
        check_command                  check_groestlcoin_blockchain!<USERNAME>!<PASSWORD>!<PORT>!<CURRENCY>!<WARN>!<CRIT>
        host_name                      <HOSTNAME>
        service_description            Groestlcoind Blockchain
        use                            generic-service
}

```

## Service Descriptions
Check Type | Description | Example output
---------- | ----------- | --------------
blockchain | Check the height of the node blockchain against [Blockexplorer](http://groestlsight.groestlcoin.org) | `CRITICAL - node block height = 2317135, global block height = 2007135`
connections | Check the number of connections (peers) reported | `OK - network connections = 8``

## Command line options
Argument | Description | Example
-------- | ----------- | -------
-u | RPC username | rpcuser
-p | RPC password | rpcpass
-H | RPC host | localhost
-P | RPC port | 1441
-B | Currency to use (only for blockchain check) - either `grs` or `tgrs` | grs
-w | Warning level to use for Nagios output | 5
-c | Critical level to use for Nagios output | 10
-t | Type of check to run - either `blockchain` or `connections` | blockchain

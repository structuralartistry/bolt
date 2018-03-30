
# Inventory File

In Bolt, you can use an inventory file to store information about your nodes.
For example, you can organize your nodes into groups or set up connection
information for nodes or node groups.

The inventory file is a yaml file stored by default at
`~/.puppetlabs/bolt/inventory.yaml`. At the top level it contains an array of
nodes and groups. Each node can have a config hash that includes values
specific to that node. Each group can have an array of nodes and a config hash.

## Inventory Config

You can only set transport configuration in the inventory file. This means
using a top level transport value to assign a transport to the target and all
values in the transports sections. You can set config on nodes or groups in the
inventory file. Bolt performs a depth first search of nodes, followed by a
search of groups, and uses the first value it finds. Nested hashes are merged.

In this inventory file example, three nodes are configured to use ssh transport
and three other nodes to use WinRM transport.

```
groups:
  - name: ssh_nodes
    nodes:
      - ssh1.example.com
      - ssh2.example.com
      - ssh3.example.com
    config:
      transport: ssh
      ssh:
        host-key-check: false
  - name: win_nodes
    nodes:
      - win1.example.com
      - win2.example.com
      - win3.example.com
    config:
      transport: winrm
      winrm:
        port: 5382
```

Override a user for a specific node.

```
nodes:
  - name: ssh1.example.com
    config:
      ssh:
        user: me
```


## Objects

The Inventory file uses the following objects.

Config
A config is a map that contains transport specific configuration options.

Group
A group is a map that requires a name and can contain a nodes : Nodes object
and/or a config : Config object. A group name must match the regular expression
values /[a-zA-Z]\w+/. These are the same values used for environments.  Groups
An array of group objects.

Node
A node can be just the string of its Node Name or a map that requires a name
key and can contain a config. For example, a node block can contain any of the
following:
```
"host1.example.com"
```
```
name: "host1.example.com"
```
```
name: "host1.example.com"
  config:
    transport: "ssh"
```

Node Name
The URI used to create the node.
Nodes
An array of node objects.

## File Format
The inventory file is a yaml file that contains a single group. This group can
be referred to as "all". In addition to the normal group fields, the top level
has an inventory file version key that defaults to 1.0.


## Precedence
When searching for node config, the URI used to create the target is matched to
the node-name. Any URI-based configuration information like host, transport or
port will override config values even those defined in the same block. For
config values, the first value found for a node is used. Node values take
precedence over group values and are searched first. Values are searched for in
a depth first order. The first item in an array is searched first.

Configure transport for nodes.


```
groups:
  - name: linux
    nodes:
      - linux1.example.com
      - linux2.example.com
      - linux3.example.com
  - name: win
    nodes:
      - win1.example.com
      - win2.example.com
      - win3.example.com
    config:
      transport: winrm
```

Configure login and escalation for a specific node.

```
nodes:
  - name: host1.example.com
    config:
      transports:
        ssh:
          user: me
          run-as: root
```

Generating inventory files
Use the bolt-inventory-pdb script to generate inventory files based on PuppetDB queries.
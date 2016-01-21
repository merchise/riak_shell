Riakshell
---------

A configurable, scriptable and extendable shell for riak

Goals
-----

The goals of riakshell are to have a single shell that can:
* run sql commands
* run riak-admin commands
* be used a developer/devops tool for managing riak clusters

To that end the shell has integral:
* logging
* log replay
* log regression replay
* config

It has three modes:
* riakshell - runs single erlang functions via an extensible architecture
* sql - executes sql commands against a remove riak node
* riak-admin - executes riak-admin commands against a remote riak node

It is intended that it will support:
* replay and regression in batch mode
  - by specifying a file of commands to replay
  - by piping in a command set
* specification of alternative configuration files at run time

The shell is also trivially extendable for developer use.

Lacunae
-------

The shell is in early stages. The following are well supported:
* extensible architecture
* logging
* replay/regression
* history
* configuration

The following are only partially supported the moment:
* sql mode
  - lexing/parsing is supported but nothing else
* riak-admin mode

The following are not yet implemented:
* riak-admin lexer/parser
* connection to remote riak nodes
* shell management (including cookies)

Running/Getting Started
-----------------------

```
cd ~/riakshell/bin
./riakshell
```

You get help on what is implemented in the riakshell with the help command:
```
help();
```

Configuration
-------------

Configuration is in the file
```
~/riakshell/etc/riakshell.config
```

The following things can be configured:
```
logging  = on | off
date_log = on | off
logfile  = "../some/dir/mylogfile.log"
```

Configuration will be exended to:
* shell mode on startup
* riak_nodes to connect to
* erlang cookie to set

Extending The Riakshell
-----------------------

Riakshell using a 'magic' architecture with convention.

Riak modules with names like:
```
mymodule_EXT.erl
```
are considered to be riakshell extension modules.

All exported functions with an arity > 1 are automaticaly exposed in riakshell mode.

To add a function which appears to the user like:
```
frobulator(bish, bash, bosh);
```

You implement a function with the following signature:
```
frobulator(#state{} = S, _Arg1, _Arg2, _Arg3) ->
    Result = "some string that is the result of the fn",
    {Result, State}.
```

Your function may modify the state record if appropriate. All the shell functions are implemented as extensions. Have a look at the different EXT files for examples.

To be a good citizen you should add a clause to the help function like:
```
-help(frobulator, 3) ->
    "This is how you use my function";
```
The second param is the arity *as it appears in the riakshell*

As a convenience to the developer there is a modules called:
```
debug_EXT.erl
```

This implements a function which reloads and reregisters all extensions:
```
load();
```
and can hot-load changes into the shell.
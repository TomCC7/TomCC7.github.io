---
title: BashScripts Notes
categories: notes
layout: post
---



# Commands

### Screen

```bash
screen -S (name)#创建名为name的screen
ctrl+a d#退出当前screen
screen -ls #列出当前screen
screen -r (name) #返回名为name的screen
```

## sed (stream editor)

```bash
sed "s?pattern?change?" # change pattern of input
sed -i "..." file # change file directly
```

##  jq (command-line json processor)

> https://cameronnokes.com/blog/working-with-json-in-bash-using-jq/

```bash
# basic usage
jq "operation" 'input'
# examples
jq . example.json # parse example.json
jq .key example.json # show the value of key in root object
jq .[2] example.json # show the second object if root is array
```

# Command Line Usages

### Run command with envs

```bash
sudo VAR=VALUE <COMMANDS>
# or
export VAR=VALUE
sudo -E <COMMAND> # -E preserve envs
```


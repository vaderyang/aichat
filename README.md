# AIChat

[![CI](https://github.com/sigoden/aichat/actions/workflows/ci.yaml/badge.svg)](https://github.com/sigoden/aichat/actions/workflows/ci.yaml)
[![Crates](https://img.shields.io/crates/v/aichat.svg)](https://crates.io/crates/aichat)

Use ChatGPT/GPT-3.5/GPT-4 in the terminal.

AIChat in chat mode:

![chat mode](https://user-images.githubusercontent.com/4012553/226499667-4c6b261a-d897-41c7-956b-979b69da5982.gif)

AIChat in command mode:

![command mode](https://user-images.githubusercontent.com/4012553/226499595-0b536c82-b039-4571-a077-0c40ad57f7db.png)

## Install

### With cargo

```
cargo install --force aichat
```

### Binaries for macOS, Linux, Windows

Download it from [GitHub Releases](https://github.com/sigoden/aichat/releases), unzip and add aichat to your $PATH.

## Features

- Support chat and command modes
- Predefine AI [roles](#roles)
- Use GPT prompt easily
- Powerful [Chat REPL](#chat-repl)
- Context-aware conversation
- Syntax highlighting markdown and 200 other languages
- Stream output with hand-typing effect
- Support multiple models
- Support proxy connection
- Dark/light theme
- Save chat messages
- Customizable API URL

## Config

On first launch, aichat will guide you through the configuration.

```
> No config file, create a new one? Yes
> OpenAI API Key: sk-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
> Use proxy? Yes
> Set proxy: socks5://127.0.0.1:1080
> Save chat messages Yes
```

On completion, it will automatically create the configuration file. Of course, you can also manually set the configuration file.

```yaml
api_key: "<YOUR SECRET API KEY>" # Request via https://platform.openai.com/account/api-keys
organization_id: "org-xxx" # optional, set organization id
model: "gpt-3.5-turbo" # optional, choose a model
temperature: 1.0 # optional, see https://platform.openai.com/docs/api-reference/chat/create#chat/create-temperature
api_url: "https://api.openai.com/v1/chat/completions" #optional, set API url to request
max_tokens: 1024 # optional, set max tokens for return from API.
save: true # optional, If set true, aichat will save chat messages to message.md
highlight: true # optional, Set false to turn highlight
proxy: "socks5://127.0.0.1:1080" # optional, set proxy server. e.g. http://127.0.0.1:8080 or socks5://127.0.0.1:1080
conversation_first: false # optional, If set true, start a conversation immediately upon repl
light_theme: false # optional, If set true, use light theme
connect_timeout: 10 # optional, Set a timeout in seconds for connect to gpt.
```

> You can use `.info` to view the current configuration file path and roles file path.

> You can use [Environment Variables](https://github.com/sigoden/aichat/wiki/Environment-Variables) to customize certain configuration items.

### Roles

We can let ChatGPT play a certain role through `prompt` to have it better generate what we want.

We can predefine a batch of roles in `roles.yaml`.

> We can get the location of `roles.yaml` through the repl's `.info` command or cli's `--info` option.

For example, we can define a role:

```yaml
- name: shell
  prompt: >
    I want you to act as a Linux shell expert.
    I want you to answer only with bash code.
    Do not provide explanations.
```

Let ChatGPT answer questions in the role of a Linux shell expert.

```
〉.role shell

shell〉 extract encrypted zipfile app.zip to /tmp/app
mkdir /tmp/app
unzip -P PASSWORD app.zip -d /tmp/app
```

We have provided many awesome [Role Examples](https://github.com/sigoden/aichat/wiki/Role-Examples).

## Chat REPL

aichat has a powerful Chat REPL.

The Chat REPL supports:

- Emacs keybinding
- Command autocompletion
- History search
- Fish-style history autosuggestion hints
- Edit/past multiline input
- Undo support

### Multi-line editing

**Type `{` or `(` at the beginning of the line to enter the multi-line editing mode.** In this mode you can type or paste multiple lines of text. Type the corresponding `}` or `)` at the end of the line to exit the mode and submit the content.

```
〉{ convert json below to toml
{
  "an": [
    "arbitrarily",
    "nested"
  ],
  "data": "structure"
}}
```

### `.help` - Print help message

```
〉.help
.info                    Print the information
.set                     Modify the configuration temporarily
.model                   Choose a model
.prompt                  Add a GPT prompt
.role                    Select a role
.clear role              Clear the currently selected role
.conversation            Start a conversation.
.clear conversation      End current conversation.
.history                 Print the history
.clear history           Clear the history
.help                    Print this help message
.exit                    Exit the REPL

Type `{` to enter the multi-line editing mode, type '}' to exit the mode.
Press Ctrl+C to abort readline, Ctrl+D to exit the REPL
```

### `.info` - View current configuration information

```
〉.info
config_file         /home/alice/.config/aichat/config.yaml
roles_file          /home/alice/.config/aichat/roles.yaml
messages_file       /home/alice/.config/aichat/messages.md
api_key             sk-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
organization_id     -
api_url             https://api.openai.com/v1/chat/completions
max_tokens          1024
model               gpt-3.5-turbo
temperature         -
save                true
highlight           true
proxy               -
conversation_first  false
light_theme         false
connect_timeout     10
dry_run             false
```

### `.set` - Modify the configuration temporarily

```
〉.set dry_run true
〉.set highlight false
〉.set save false
〉.set temperature 1.2
```

### `.model` - Choose a model

```
> .model gpt-4
> .model gpt-4-32k
> .model gpt-3.5-turbo
> .model gpt-3.5-turbo-16k
```

### `.prompt` - Set GPT prompt

When you set up a prompt, every message sent later will carry the prompt.

```
〉{ .prompt
I want you to translate the sentences I write into emojis.
I will write the sentence, and you will express it with emojis.
I just want you to express it with emojis.
I want you to reply only with emojis.
}
Done

Ｐ〉You are a genius
👉🧠💡👨‍🎓

Ｐ〉I'm embarrassed
🙈😳
```

`.prompt` actually creates a temporary role internally, so **run `.clear role` to clear the prompt**.

When you are satisfied with the prompt, add it to `roles.yaml` for later use.

### `.role` - Let the AI play a role

Select a role:

```
〉.role emoji
name: emoji
prompt: I want you to translate the sentences I write into emojis. I will write the sentence, and you will express it with emojis. I just want you to express it with emojis. I don't want you to reply with anything but emoji. When I need to tell you something in English, I will do it by wrapping it in curly brackets like {like this}.
temperature: null
```

AI takes the role we specified:

```
emoji〉hello
👋
```

Clear current selected role:

```
emoji〉.clear role

〉hello
Hello there! How can I assist you today?
```

### `.conversation` - start a context-aware conversation

By default, aichat behaves in a one-off request/response manner.

You can run `.conversation` to enter context-aware mode, or set `config.conversation_first` true to start a conversation immediately upon repl.

```
〉.conversation

＄list 1 to 5, one per line                                                              4089
1
2
3
4
5

＄reverse the list                                                                       4065
5
4
3
2
1

```

When entering conversation mode, prompt `〉` will change to `＄`. A number will appear on the right,
indicating how many tokens are left to use.
Once the number becomes zero, you need to start a new conversation.

Exit conversation mode:

```
＄.clear conversation                                                                    4043

〉
```

## License

Copyright (c) 2023 aichat-developers.

aichat is made available under the terms of either the MIT License or the Apache License 2.0, at your option.

See the LICENSE-APACHE and LICENSE-MIT files for license details.

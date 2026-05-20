# OpenClaw

## Prerequisites

Install nvm:

```
# Install nvm (if you don't have it)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

# Load nvm into your current session
source ~/.bashrc

# Install and use Node.js 24
nvm install 24
nvm use 24

# Verify the version
node -v # Should now show v24.x.x
```

Then, install Homebrew by running the following commands:

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
and finish Homebrew installation by following the recommended *Next Steps* (printed at the end of the installation):

```
==> Next steps:
- Run these commands in your terminal to add Homebrew to your PATH:
    echo >> /home/nassos/.bashrc
    echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv bash)"' >> /home/nassos/.bashrc
    eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv bash)"
- Install Homebrew's dependencies if you have sudo access:
    sudo apt-get install build-essential
  For more information, see:
    https://docs.brew.sh/Homebrew-on-Linux
- We recommend that you install GCC:
    brew install gcc
- Run brew help to get started
- Further documentation:
    https://docs.brew.sh
```

### Run the OpenClaw Installer

Run the installer and skip installing any skills.

```
curl -fsSL https://openclaw.ai/install.sh | bash
```

Add it to PATH:

```
echo 'export PATH="$HOME/.local/bin:$HOME/.nvm/versions/node/v24.14.0/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```


### Recommendations

```
openclaw security audit --deep
openclaw security audit --fix
openclaw doctor --repair
```

## Connect through Telegram

Configure the bot connection and set up the gateway:
```
openclaw config set channels.telegram.botToken "YOUR_BOT_TOKEN"
openclaw config set channels.telegram.enabled true
openclaw config set channels.telegram.allowFrom '["TelegramIDNumber"]'
openclaw gateway install
openclaw gateway start
```

Pair OpenClaw with your Telegram Bot:

1. Open Telegram and find your bot.
2. Send the message: /start
3. The bot will reply with a Pairing Code (e.g., XY1234).
4. Back in your VPS terminal, run:

```
openclaw pairing approve telegram <YOUR_CODE>
```

## Uninstall OpenClaw

```
openclaw gateway stop
openclaw gateway uninstall
pkill -f openclaw
npm uninstall -g openclaw clawhub
rm -rf ~/.openclaw
rm -rf ~/.config/systemd/user/openclaw-gateway.service
rm -rf /tmp/openclaw-$(id -u)
sed -i '/.openclaw\/completions/d' ~/.bashrc ~/.profile
hash -r
```
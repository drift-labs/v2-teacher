<div align="center">
  <img height="120x" src="https://uploads-ssl.webflow.com/611580035ad59b20437eb024/616f97a42f5637c4517d0193_Logo%20(1)%20(1).png" />

  <h1 style="margin-top:20px;">Drift v2 API Docs</h1>

  <p>
    <a href="https://drift-labs.github.io/v2-teacher"><img alt="API Docs" src="https://img.shields.io/badge/docs-tutorials-blueviolet" /></a>
    <a href="https://discord.com/channels/849494028176588802/878700556904980500"><img alt="Discord Chat" src="https://img.shields.io/discord/889577356681945098?color=blueviolet" /></a>
    <a href="https://opensource.org/licenses/Apache-2.0"><img alt="License" src="https://img.shields.io/github/license/project-serum/anchor?color=blueviolet" /></a>
  </p>
</div>

# Drift v2 API Docs

This repository contains the API docs for interacting with the Drift v2.

# Local Dev Setup

Native setup for MacOS (you may also want to refer to the [original Slate wiki](https://github.com/slatedocs/slate/wiki/Using-Slate-Natively) for the original instructions and for other OSes)

Make sure latest ruby version is installed:

```
brew update
brew install ruby
```

(mac users will need to make sure their [`PATH` is set properly](https://mac.install.guide/ruby/13.html) to not use the default system ruby)

Update gem (no `sudo`, if it fails running it again seems to work...):

```
gem update --system
```

Install bundler:

```
export GEM_HOME="$HOME/.gem"
gem install bundler
```

Install Nokigiri dependencies:

```
brew install libxml2 libxslt
```

If using m1 mac:

```
bundle config set force_ruby_platform true
```

Build the docs

```
bundle install
```

After doing the above, you can work on docs locally via:

```
bundle exec middleman server
```

or

```
./start.sh
```

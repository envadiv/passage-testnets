# Passage - deran-1 Testnet

This unincentivized testnet will start at juno `v1.1.0`.

## Setup

**Prerequisites:** Make sure to have [Golang >=1.19](https://golang.org/).

### Minimum hardware requirements

- 8-16GB RAM
- 200GB of disk space
- 2 cores

#### Go setup

You need to ensure your gopath configuration is correct. If the following **'make'** step does not work then you might have to add these lines to your .profile or .zshrc in the users home folder:

```sh
nano ~/.profile
```

```
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
```

Source update .profile

```sh
source .profile
```

#### Download Passage

```sh
git clone https://github.com/envadiv/Passage3D
cd Passage3D
git checkout tags/v1.1.0
make build && make install
```

Check that you have the right Passage version installed:

```sh
$ passage version --long
name: passage
server_name: passage
version: 1.1.0
commit: e00e301891a4a9859f2cc70d5a5bd40d664a0f32
build_tags: netgo,ledger
go: go version go1.19.5 darwin/arm64
```

## Setup validator node

Below are the instructions to generate & submit your genesis transaction

### Generate genesis transaction (gentx)

1. Initialize the Passage directories and create the local genesis file with the correct chain-id:

```bash
passage init <moniker-name> --chain-id=deran-1
```

2. Create a local key pair (skip this step if you already have a key):

```sh
passage keys add <key-name>
```

3. Add your account to your local genesis file with a given amount and the key you just created. Use only `10000000000upasgx`, other amounts will be ignored.

```bash
passage add-genesis-account $(passage keys show <key-name> -a) 10000000000upasgx
```

4. Create the gentx, use only `9000000000upasgx`:

```bash
passage gentx <key-name> 9000000000upasgx --chain-id=deran-1
```

If all goes well, you will see a message similar to the following:

```bash
Genesis transaction written to "${HOME}/.passage/config/gentx/gentx-******.json"
```

5. Change minimum gas prices in `app.toml` to `0.0025upasgx`.

### Submit genesis transaction

- Fork [the testnets repo](https://github.com/envadiv/passage-testnet) into your Github account

- Clone your repo using

  ```bash
  git clone https://github.com/<your-github-username>/testnets
  ```

- Copy the generated gentx json file to `<repo_path>/deran-1/gentx/`. If you want, rename it to `<your-validator-moniker>.json` so it's easier to identify.

  ```sh
  > cd testnets
  > cp "${HOME}/.juno/config/gentx/gentx*.json" ./deran-1/gentx/
  ```

- Commit and push to your repo
- Create a PR onto https://github.com/envadiv/passage-testnets
- Only PRs from individuals / groups with a history successfully running nodes will be accepted. This is to ensure the network successfully starts on time.

#### Running in production

**Note, as usual we'll be going through some upgrades for this testnet. Consider using [Cosmovisor](https://github.com/cosmos/cosmos-sdk/tree/master/cosmovisor) to make your life easier.** Setting up Cosmovisor is covered in the [Juno Documentation](https://docs.junochain.com/validators/setting-up-cosmovisor).

Download Genesis file when the time is right. Put it in your `${HOME}/.juno` folder.

If you have not installed cosmovisor, create a systemd file for your Juno service:

```sh
sudo nano /etc/systemd/system/passage.service
```

Copy and paste the following and update `<YOUR_USERNAME>`:

```sh
Description=Juno daemon
After=network-online.target

[Service]
User=juno
ExecStart=/home/<YOUR_USERNAME>/go/bin/passage start
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
```

Enable and start the new service:

```sh
sudo systemctl enable passage
sudo systemctl start passage
```

Check status:

```sh
passage status
```

Check logs:

```sh
journalctl -u passage -f
```

## Once the Genesis has been Generated.

**Genesis File**

[Genesis File](/deran-1/genesis.json):

```bash
   curl -s  https://raw.githubusercontent.com/envadiv/Passage3D/main/deran-1/genesis.json > ~/.juno/config/genesis.json
```

**Genesis sha256**

```bash
sha256sum "${HOME}/.juno/config/genesis.json"
# f14bf43d43e69d470859d2ba1e1eee6d576229aeb1e7c26cc98d29254886820a
```

**passage version**

```bash
$ passage version --long
name: juno
server_name: passage
version: v9.0.0
commit: e6f9629538a88edf11aa7e7ed3d68c61f8e96aa6
build_tags: netgo,ledger
```

**Seed nodes**

```
TBD
```

**Persistent Peers**

```
TBC
```

### Learn more

- [Cosmos Network](https://cosmos.network)
- [Juno Documentation](https://docs.junochain.com/)

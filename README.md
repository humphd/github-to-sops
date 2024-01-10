This script enables one to use github as a trusted public key distribution mechanism.

I needed github-to-sops to make [sops](https://github.com/getsops/sops) easier to use for my https://deepstructure.io and https://chatcraft.org projects.

I use https://github.com/Mic92/ssh-to-age/ (until the [sops ssh backend](https://github.com/getsops/sops/pull/1134) lands).

This generates a nice .sops.yaml file with comments indicating where the keys came from to make key rotation easier.

## Examples:

For generating sops from github:

```bash
./github-to-sops --github-url https://github.com/tarasglek/chatcraft.org --key-types ssh-ed25519 --format sops
creation_rules:
  - key_groups:
      - age:
        - age19j4d6v9j7rx5fs629fu387qz4zmlpsqjexa4s08tkfrrmfdl5cwqjlaupd # humphd
        - age13runq29jhy9kfpaegczrzttykerswh0qprq59msgd754yermtfmsa3hwg2 # tarasglek
```

Generate keys from a local github checkout and add ssh hosts to it:

```bash
./github-to-sops --local-github-checkout . --format sops --known-hosts ~/.ssh/known_hosts --key-types ssh-ed25519
creation_rules:
  - key_groups:
      - age:
        - age13runq29jhy9kfpaegczrzttykerswh0qprq59msgd754yermtfmsa3hwg2 # tarasglek
        - age120ld5rvtsuavnlexa2kc7eahrg8egf4gwg22t0q44rcu2z3xegrq4364t4 # 192.168.1.1
```

For generating ssh authorized_keys:

```bash
./github-to-sops --github-url https://github.com/tarasglek/chatcraft.org --format authorized_keys
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIedvn21UgBc1VcasThd+/U84Xfkrw+Ox5RIxufs5tJP humphd
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDzJmAWAOp6fQGs/+v1PT+0dgzG7XHwhJnvF+tL5TwJx tarasglek
```

## Usage:
```
./github-to-sops -h
usage: github-to-sops [-h] [--github-url GITHUB_URL | --local-github-checkout LOCAL_GITHUB_CHECKOUT] [--known-hosts KNOWN_HOSTS] [--github-users GITHUB_USERS] [--key-types KEY_TYPES] [--format {authorized_keys,ssh-to-age,sops}]

Fetch SSH keys of GitHub repository contributors or specified github users and output that info into a useful format like sops or ssh authorized_keys

options:
  -h, --help            show this help message and exit
  --github-url GITHUB_URL
                        GitHub repository URL.
  --local-github-checkout LOCAL_GITHUB_CHECKOUT
                        Path to local Git repository.
  --known-hosts KNOWN_HOSTS
                        Path to ssh known hosts to also fetch keys from
  --github-users GITHUB_USERS
                        Comma-separated list of GitHub usernames to fetch keys for.
  --key-types KEY_TYPES
                        Comma-separated types of SSH keys to fetch (e.g., ssh-ed25519,ssh-rsa). Pass no value for all types.
  --format {authorized_keys,ssh-to-age,sops}
                        Output/convert keys using the specified format. Supported formats: authorized_keys, ssh-to-age, sops. For example, use '--format ssh-to-age' to convert SSH keys to age keys.

Example invocations: `./github-to-sops --github-url https://github.com/tarasglek/chatcraft.org --key-types ssh-ed25519 --format sops` `./github-to-sops --github-url https://github.com/tarasglek/chatcraft.org --format authorized_keys` `./github-to-sops --local-github-
checkout . --format sops --known-hosts ~/.ssh/known_hosts --key-types ssh-ed25519`
```

## Env vars:
*  GITHUB_TOKEN: optional github token which helps avoid rate limiting.
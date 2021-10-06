# $FORK ForkDot - KodaDot based NFT Explorer for Kusama & Polkadot ecosystem
<!-- ALL-CONTRIBUTORS-BADGE:START - Do not remove or modify this section -->
[![All Contributors](https://img.shields.io/badge/all_contributors-25-orange.svg?style=flat-square)](#contributors-)
[![DeepScan grade](https://deepscan.io/api/teams/13903/projects/16948/branches/372223/badge/grade.svg)](https://deepscan.io/dashboard#view=project&tid=13903&pid=16948&bid=372223)
[![DeepSource](https://deepsource.io/gh/kodadot/nft-gallery.svg/?label=active+issues&show_trend=true)](https://deepsource.io/gh/kodadot/nft-gallery/?ref=repository-badge)
<!-- ALL-CONTRIBUTORS-BADGE:END -->

## 🧫 Culture - where you can read our recent updates
* [Discord](https://discord.gg/)
* [Telegram](https://t.me/kodadot)
* [Twitter](https://twitter.com/ForkDot)
* [r/ForkDot](https://www.reddit.com/r/ForkDot/)

## 📚 Writings by the KodaDot team
* [Client-first NFT gallery: Technical examination](https://medium.com/kodadot/client-first-nft-gallery-technical-examination-33db09dfdc97)
* [How to Embed your NFT on Kusama through ForkDot](https://medium.com/kodadot/how-to-embed-your-nft-on-kusama-through-kodadot-ee52c2384b0d)
* [Traverse to the prime show](https://medium.com/kodadot/traverse-to-the-prime-show-733d6046d3f5)
* [The First Multilingual NFT Gallery in Polkadot ecosystem running live on Kusama](https://medium.com/kodadot/the-first-multilingual-nft-gallery-in-polkadot-ecosystem-running-live-on-kusama-b8f7566770be)
* [Read our story, how we started.](https://medium.com/kodadot/kodadot-nft-explorer-f2c3a326a856)


## Working version ▶️

* [Explore and Mint NFTs](https://nft.kodadot.xyz/)

## Development 🏗

[Contribution is welcome!](CONTRIBUTING.md)

We are using `yarn` for scripts, installing things via npm **will result in broken dependencies.**

## Contributors ✨

Thanks goes to the wonderful people at Kusama ([emoji key](https://allcontributors.org/docs/en/emoji-key)):

[Forked from KodaDot](https://github.com/kodadot/nft-gallery/graphs/contributors)


<!-- markdownlint-restore -->
<!-- prettier-ignore-end -->

<!-- ALL-CONTRIBUTORS-LIST:END -->

## 🕹 Play

```shell
git clone git@github.com:kodadot/nft-gallery.git
yarn
yarn serve
open http://localhost:9090/
```

## 🙋‍♀️ I want to contribute

Sure, your **contribution** is welcome. Please follow [code of conduct](CODE_OF_CONDUCT.md) and [contribution guidelines](CONTRIBUTING.md)

## Support the project ⭐
If you feel awesome and want to support us in a small way, please consider starring and sharing the repo! This helps us getting known and grow the community. 🙏


## 🐳 Docker
If you just want to try out our ForkDot on Kusama and have a full local setup with a local node, we assume you have [docker](https://docs.docker.com/get-docker/) and docker-compose installed. We have are building [images from develop and master branch](https://hub.docker.com/r/yangwao/kodadot/tags?page=1&ordering=last_updated)

```
docker-compose pull && docker-compose up
```

If you want to run just ForkDot
```
docker-compose up kodadot
```

Build docker image of ForkDot
```
docker build -t hello/kodadot .
```

Run it locally and then visit `localhost:9090`
```
docker run -it -p 8080:8080 --rm --name hellokodadot hello/kodadot
```


## Dev hints

In order to execute some transaction you can use `exec` located in `src/utils/transactionExecutor.ts`
Usage:
```js
import exec from '@/utils/transactionExecutor';

// arguments: from which account, password for account, which action, array of parameters
this.tx = await exec(this.account, this.password, api.tx.democracy.vote, [referendumId, { aye, conviction }]);
```

#### Using reactive properties
Some of the properties on the component needs to be automatically updated (currentBlock)

Usage:
```html
<template>
  <div>{{ currentBlock  }}</div>
</template>

<script lang="ts">
// Skipping imports
export default class Summary extends Vue {
  private currentBlock: any = {};
  private subs: any[] = [];

  public async mounted() {
    this.subs.push(await api.derive.chain.bestNumber(value => this.currentBlock = value));
  }

  // Unsubscribe before destroying component
  public beforeDestroy() {
    this.subs.forEach((sub) => sub());
  }
}

</script>
```

# 🏃‍♀️ Quick Setup

Here is a quick setup guide for the project.

```bash
git clone https://github.com/kodadot/nft-gallery.git
touch .env.local
```

in `.env.local` add following properties:
```bash
VUE_APP_KEYRING=true
VUE_APP_I18N_LOCALE=en
VUE_APP_I18N_FALLBACK_LOCALE=en
VUE_APP_SUBQUERY_URL=https://api.subquery.network/sq/vikiival/magick-west
```
[You can obtain some Westend (WND)](https://matrix.to/#/#westend_faucet:matrix.org)

If you want to access Kusama's GraphQL API, **remove** `-west` from `VUE_APP_SUBQUERY_URL`

> to run UI

```bash
yarn
yarn start
```

> in a second terminal window:
> this will run lambda functions

```bash
yarn lambda
```

### Caveats:
Netlify functions are **unable** to read `.env.local`.
Therefore, you need to manually update pinata keys in each function.
Functions are located in `src-functions/`

[You can obtain Master Pinata Keys here](https://app.pinata.cloud/keys)

## Running local Polkadot and subquery nodes

To run the full local environment we recommend you to run a [polkadot/Kusama node](https://github.com/paritytech/polkadot).
In case you are using Apple M1, we have a [tutorial for that 🍏 ](https://vikiival.medium.com/run-substrate-on-apple-m1-a2699743fae8)

To run also a subquery indexing node please [check this repo](https://github.com/vikiival/magick)

Moreover please add this to your `.env.local`

```bash
VUE_APP_SUBQUERY_URL=http://localhost:3000
```

### Linting code
#### Show all problems
```bash
yarn lint
```
#### Show only errors
```bash
yarn lint --quiet
```
#### Fix errors
```bash
yarn lint --fix
```

### Dev hints

In order to execute some transaction you can use `exec` located in `src/utils/transactionExecutor.ts`
Usage:
```js
import exec from '@/utils/transactionExecutor';

// arguments: from which account, password for account, which action, array of parameters
this.tx = await exec(this.account, this.password, api.tx.democracy.vote, [referendumId, { aye, conviction }]);
```

#### Using reactive properties
Some of the properties on the component needs to be automatically updated (currentBlock)

Usage:
```html
<template>
  <div>{{ currentBlock  }}</div>
</template>

<script lang="ts">
// Skipping imports
export default class Summary extends Vue {
  private currentBlock: any = {};
  private subs: any[] = [];

  public async mounted() {
    this.subs.push(await api.derive.chain.bestNumber(value => this.currentBlock = value));
  }

  // Unsubscribe before destroying component
  public beforeDestroy() {
    this.subs.forEach((sub) => sub());
  }
}

</script>
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).


### Generating changelog

To generate changelog use github cli
List only merged, if you need limit use `-L`

```
gh pr list -s merged --json mergedAt,baseRefName,number,title,headRefName -B main -L 37 | jq -r '.[] | .number, .title' | sed '/^[0-9]/{N; s/\n/ /;}'
```

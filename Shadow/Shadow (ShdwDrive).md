# Shadow

## Value Proposition

Shadow sits between other decentralized/distributed storage solutions by trying to be sort of the "everything" storage project, being able to function as a high speed CDN, an archival system, personal storage solution, etc. Basically, a bundle of IPFS, $FIL and $AR altogether!

From what we can gleam from their docs and blogs, Shadow aims to be:
- User friendly SDK that can be integrated into projects easily
- "Reliable" storage that scales through "robust CDN design"
- Ability to choose between mutable and immutable data storage
- Able to function as:
	- a CDN with S3 compatibility
	- a data archive with "x3 replication, secure erasure and encryption"
	- a personal Dropbox like drive

Shadow also claims that it's base implementation, named *DAGGER*, can also be extended to operate as an oracle/orchestration protocol to enable it to function as a compute provider (in the DigitalOcean Droplets/Amazon EC2 instances sense, so closer to Akash, further from AO). Lots of big claims being thrown around!

As it stands, Shadow is currently on its second phase of its Testnet (which introduces incentives in the form of $SHDW tokens) which implements this DAGGER framework (which we dub as shdwDrive v2) while having a live implementation (being shdwDrive v1).

## Addressing shdwDrive v1

The current live production version of Shadow that people are free to interact with is said to be built atop [Ceph](https://en.wikipedia.org/wiki/Ceph_(software)), which is an existing distributed file storage solution that has found usage in services such as DigitalOcean and projects like [literally CERN](https://cephdocs.s3-website.cern.ch/ops/clusters/index.html). We're not here to enable stolen valor however — Ceph is a brilliant piece of technology used by many, but _this is something that the Shadow developers simply forked and modified_.

Furthermore, from my understanding, shdwDrive v1 relies entirely on interactions with their API hosted on https://shdw-drive.genesysgo.net/. Yes, there exists Solana contracts to do with the project at present, but it for sure isn't some _Solana based implementation of Ceph_.

There isn't a way to become a node operator to participate in their Ceph cluster, so what we're dealing with very much is just an API endpoint to a centralised cluster hosted by the Shadow team that happens to have interactions with it reflected on Solana, which makes it no different from just uploading files to Google Drive or something (except if they are running a Ceph cluster as advertised, then your files are sort of doubly safe. Also, besides a 1Gb filesize limit, they wouldn't be prone to content restrictions, which is always wonderful).
### The Solana Parts

So let's actually dig into what has been implemented for shdwDrive v1 that has to do with Solana and the $SHDW token.

Fortunately, the [Typescript SDK](https://github.com/GenesysGo/shadow-drive/tree/main) gives us a view into the various contract interactions that can be performed and thankfully an [IDL (Interface Definition Language)](https://github.com/GenesysGo/shadow-drive/blob/a9ad4f0b883a242194c86d1d8ab8321ab57a5726/src/utils/idl.ts#L1) for the main ShadowDrive contract is available. There's no contract source, but with the IDL we can infer all the operations it would conduct.

There's like... actually basically nothing to it?

You first [create a Storage Account](https://github.com/GenesysGo/shadow-drive/blob/main/src/methods/create-storage-account.ts) and a Staking Account, where your Storage Account is supposed to be a parent to a bunch of child File Accounts (although this doesn't seem to be implemented in practice?) and your Staking Account is what holds your storage fees/stake (explicitly stated in the [StorageConfig](https://github.com/GenesysGo/shadow-drive/blob/a9ad4f0b883a242194c86d1d8ab8321ab57a5726/src/types/accounts/StorageConfig.ts) spec). 


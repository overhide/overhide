# Data Decentralization

The [*overhide* broker reference implementation](https://github.com/JakubNer/overhide-broker) uses [*IPFS*](https://ipfs.io/) to decentralize persisted data.

However, since this core value is implementation specific, not every *overhide* broker may keep user data decentralized.  If an implementation of an *overhide* broker does not openly decentralize user data, the user can always leverage tooling that uses the [import/export functionality of *overhide* broker APIs](broker.html#tag-import-export) to extract all user data and do with it as the user pleases.  That's a contractual obligation on a proper *overhide* broker implementation.

![import/export only /* show one overhide broker being used to export data to a browser and being pushed into another broker */](images/import-export.gif) 

The [import/export functionality](broker.html#tag-import-export) may prove to be a somewhat cumbersome failover mechanism.  The intent of *overhide* is to go as far as possible to bring decentralization to our apps, with the hope that one day the broker is completely out of the picture and only *overhide.js* remains as a library, not a client shim. 

![overhide.js as library /* show colored lines from broker to blockchain and ipfs flying into browser and becoming colored dots in there, as browser talks directly to blockchain and ipfs */](images/oh-js-as-lib.gif) 

## Overview of [*overhide* Reference Implementation](https://github.com/JakubNer/overhide-broker) and [*IPFS*](https://ipfs.io/)

All user data and data-specific metadata is stored on the [*IPFS*](https://ipfs.io/) network.

Broker specific metadata--e.g. configuration and session information--is not stored on the [*IPFS*](https://ipfs.io/) network.

A broker facilitates a connection to [*IPFS*](https://ipfs.io/) and enforces an *overhide* specific data structure on [*IPFS*](https://ipfs.io/).  It enables current *Web 2.0* services to leverage [*IPFS*](https://ipfs.io/) through the *overhide* abstraction.

![overhide broker as facilitator to IPFS /* app uses JSON to push objects into overhide which stores files into a nice structure, ipfs is in a cloud, other overhide instances reference the same cloud*/](images/oh-facilitator.gif)

Each broker has its own instance-local folder--we'll call it `/overhide-repo`--dedicated to all the user data.  This folder is constantly updated in [*IPFS*](https://ipfs.io/) and [*IPNS*](https://docs.ipfs.io/guides/concepts/ipns/) as users work with their data.  Ideally, on each change of the broker's data, the [*IPFS*](https://ipfs.io/) merkle-dag is updated and the [*IPNS*](https://docs.ipfs.io/guides/concepts/ipns/) snapshot is re-referenced.  Realistically the [*IPFS*](https://ipfs.io/) network--and especially the slower [*IPNS*](https://docs.ipfs.io/guides/concepts/ipns/) reference to the most recent data--is only eventually consistent as seen by other brokers.

![delay in distributing user data /* user works with data in /overhide-repo and files, ipfs points to /oh-repo with a clock beside the line, ipns points to ipfs, other broker peers point to ipns and have a clock beside their lines, as well as a googly eyes as in 'looking', there are data bits flying between user and /oh-repo much faster than the googly bits being updated one way in ipfs, even more delay for other nodes */](images/delay-in-distro.gif)

As each broker updates its `/overhide-repo` to [*IPFS*](https://ipfs.io/), it necessarily pins all the content, and re-publishes to [*IPNS*](https://docs.ipfs.io/guides/concepts/ipns/) under its [*IPFS*](https://ipfs.io/) node *PeerId* (see [`ipfs name publish`](https://ipfs.io/docs/commands/#ipfs-name-publish)).  The broker makes its *PeerId* available to the app (service) [via APIs](broker.html#operation--peer-ids-get).  Armed with the *PeerId* the user can use tooling to directly access their most recent data on [*IPFS*](https://ipfs.io/)--allowing for publishing delay--or [direct their app (service) to use a different broker](broker.html#operation--all-data-post) to work with their data.

#### `/overhide-repo/[*Identity*](identity.md)` Child Directories

An [*identity*](identity.md) is a token calculated client-side--in *overhide.js*.  To an *overhide* broker an [*identity*](identity.md) certifies a user owns the public key used for broker remuneration.  It is also used to create a directory to contain all the data, metadata, and backchannel queues for [*segment-keys*](glossary.md#segment-key) owned by the user.

All users' data in `/overhide-repo`--the [*IPNS*](https://docs.ipfs.io/guides/concepts/ipns/) content-root--sits in an immediate child directory with the [*identity*](identity.md) as the directory name.  As such, each user's data is navigable via [*IPNS*](https://docs.ipfs.io/guides/concepts/ipns/) by dereferencing *PeerId*/[*identity*](identity.md).

The [*identity*](identity.md) is a function of the user's public key and some user defined passphrase; as such the data in [*IPFS*](https://ipfs.io/) belonging to some public-key is  pseudonymous.

#### Data as Files

A broker writes data to files under */overhide-repo/[identity](identity.md)/* with [*segment-keys*](glossary.md#segment-key) for file names.

The data is encrypted in a [usage specific way](glossary.md#datastore-value-secret).

#### [Messages](glossary.md#backchannel-queue) as Files

All backchannel queues for an identity are filed under */overhide-repo/[identity](identity.md)/backchannel/* with the [*segment-key*](glossary.md#segment-key) of the queue as file name.

The messages are encrypted in a [usage specific way](glossary.md#datastore-value-secret), decipherable by recipient upon receipt.

#### Access Metadata as Files

Metadata for a specific [*segment-key*](glossary.md#segment-key) lives under */overhide-repo/[*identity*](identity.md)/metadata/* with [*segment-key*](glossary.md#segment-key) as file name.

This file is not meant for consumption by the user, but by *overhide* systems; these files are plain-text with pseudonymous content.
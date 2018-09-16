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

#### [*Identity*](identity.md) Child Directories

An [*identity*](identity.md) is a token calculated client-side--in *overhide.js*.  To an *overhide* broker an [*identity*](identity.md) certifies a user owns the public key used for broker remuneration.  It is also used to create a directory to contain all the data, metadata, and backchannel queues for [*segment-keys*](glossary.md#segment-key) owned by the user.

Each broker is a steward of its users' data in an instance-local repository (*/repo*).  This repository has a folder for each of the users with their [*identity*](identity.md) as the directory name.  These folders are constantly updated in [*IPFS*](https://ipfs.io/) and [*IPNS*](https://docs.ipfs.io/guides/concepts/ipns/) as users work with their data--this necessarily means that each [*identity*](identity.md) has its own private-public [*IPNS*](https://docs.ipfs.io/guides/concepts/ipns/) key-pair.  The public key constitutes a *GUID* (globally unique ID), which is the user's data content-root on the [*IPNS*](https://docs.ipfs.io/guides/concepts/ipns/) network.  Ideally, on each change of the user's data, the [*IPFS*](https://ipfs.io/) merkle-dag is updated and the [*IPNS*](https://docs.ipfs.io/guides/concepts/ipns/) snapshot is re-referenced.  Realistically the [*IPFS*](https://ipfs.io/) network--and especially the slower [*IPNS*](https://docs.ipfs.io/guides/concepts/ipns/) reference to the most recent data--is only eventually consistent as seen by other brokers.

![delay in distributing user data /* user works with data in /overhide-repo and files, ipfs points to /oh-repo with a clock beside the line, ipns points to ipfs, other broker peers point to ipns and have a clock beside their lines, as well as a googly eyes as in 'looking', there are data bits flying between user and /oh-repo much faster than the googly bits being updated one way in ipfs, even more delay for other nodes */](images/delay-in-distro.gif)

As each user modifies their data, the broker updates [*IPFS*](https://ipfs.io/), necessarily pins all the content, and re-publishes to [*IPNS*](https://docs.ipfs.io/guides/concepts/ipns/) under the user's [*IPNS*](https://docs.ipfs.io/guides/concepts/ipns/) public-key hash (see [`ipfs name publish`](https://ipfs.io/docs/commands/#ipfs-name-publish))--the *GUID*.  The broker makes the user's *GUID* available to the app (service) [via APIs](broker.html#operation--guids-get).  Armed with the *GUID* the user can use tooling to directly access their most recent data on [*IPFS*](https://ipfs.io/)--allowing for publishing delay--or [direct their app (service) to use a different broker](broker.html#operation--all-data-post) to work with their data.

#### Data as Files

A broker writes data to local files under *//repo/[identity](identity.md)/* with [*segment-keys*](glossary.md#segment-key) for file names.

This data is navigable via [*IPNS*](https://docs.ipfs.io/guides/concepts/ipns/) by dereferencing *GUID/[*segment-key*](glossary.md#segment-key)* where *GUID* is the [*identities*](identity.md) [*IPNS*](https://docs.ipfs.io/guides/concepts/ipns/) public-key hash.

The data is encrypted in a [usage specific way](glossary.md#datastore-value-secret).

#### [Messages](glossary.md#backchannel-queue) as Files

All backchannel queues for an identity are filed under *//repo/[identity](identity.md)/backchannel/* with the [*segment-key*](glossary.md#segment-key) of the queue as file name.

Each queue is navigable via [*IPNS*](https://docs.ipfs.io/guides/concepts/ipns/) by dereferencing *GUID/backchannel/[*segment-key*](glossary.md#segment-key)*.

The messages are encrypted in a [usage specific way](glossary.md#datastore-value-secret), decipherable by recipient upon receipt.

#### Access Metadata as Files

Metadata for a specific [*segment-key*](glossary.md#segment-key) lives under *//repo/[*identity*](identity.md)/metadata/* with [*segment-key*](glossary.md#segment-key) as file name.

Each queue is navigable via [*IPNS*](https://docs.ipfs.io/guides/concepts/ipns/) by dereferencing *GUID/metadata/[*segment-key*](glossary.md#segment-key)*.

This file is not meant for consumption by the user, but by *overhide* systems; these files are plain-text with pseudonymous content.
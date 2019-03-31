# Minimally Viable Product Feature Set

> ETA: a number of months

The following features are planned as part of the first release.

The rest of the features will be put on the backlog and implemented in the future after MVP release, namely:

* stewardship
* migration
* delegation
* metrics

All feature assume inclusion of both relevant [broker API](https://overhide.github.io/overhide/docs/broker.html) implementation and relevant [overhide.js](overhide.js.md) implementation.

## Features Planned for MVP

### ledgers.js

* implement [keybiner](secrets-keybiner.md#keybiner)

### overhide.js

* [secrets](glossary.md#secrets)

### broker + overhide.js

* [capabilities](https://overhide.github.io/overhide/docs/broker.html#tag-capabilities)
* [flow-control](https://overhide.github.io/overhide/docs/broker.html#tag-flow-control)
* [broker-lookup](https://overhide.github.io/overhide/docs/broker.html#tag-broker-lookup)
* portions of [auth](https://overhide.github.io/overhide/docs/broker.html#tag-auth)
    * [PUT /auth/credentials](https://overhide.github.io/overhide/docs/broker.html#operation--auth-credentials-put)
    * [GET /auth/authorities](https://overhide.github.io/overhide/docs/broker.html#operation--auth-authorities-get)
    * [PUT /auth/changed-credentials](https://overhide.github.io/overhide/docs/broker.html#operation--auth-changed-credentials-put)
    * [GET /auth/{remuneration-key}/{from-address}/{to-address}/transactions](https://overhide.github.io/overhide/docs/broker.html#operation--auth--remuneration-key---from-address---to-address--transactions-get)
* [subletting](https://overhide.github.io/overhide/docs/broker.html#tag-subletting)
* [data](https://overhide.github.io/overhide/docs/broker.html#tag-data)
* [backchannel queues](https://overhide.github.io/overhide/docs/broker.html#operation-post-message-WIRE)
* portions of [data-stewardship](https://overhide.github.io/overhide/docs/broker.html#tag-data-stewardship):
    * [GET /{segment-key}/persistence-status](https://overhide.github.io/overhide/docs/broker.html#operation---segment-key--persistence-status-get)
    * [PUT /guids](https://overhide.github.io/overhide/docs/broker.html#operation--guids-put)
    * [GET /guids](https://overhide.github.io/overhide/docs/broker.html#operation--guids-get)
    * [GET /latest-snapshot-id/{guid}](https://overhide.github.io/overhide/docs/broker.html#operation--latest-snapshot-id--guid--get)
* portions of [metrics](https://overhide.github.io/overhide/docs/broker.html#tag-metrics)
    * [WIRE segment-keys](https://overhide.github.io/overhide/docs/broker.html#operation-segment-keys-WIRE)

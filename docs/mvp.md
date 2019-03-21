# Minimally Viable Product Feature Set

> ETA: a number of months

The following features are planned as part of the first release.

The rest of the features will be put on the backlog and implemented in the future after MVP release, namely:

* stewardship
* migration
* delegation
* metrics

All feature assume inclusion of both relevant [broker API](broker.html) implementation and relevant [overhide.js](overhide.js.md) implementation.

## Features Planned for MVP

### ledgers.js

* implement [keybiner](glossary.html#keybiner--keyrings)
* integrate into [ohledger](https://ohledger.com)

### overhide.js

* [secrets](glossary.html#secrets)

### broker + overhide.js

* [capabilities](broker.html#tag-capabilities)
* [flow-control](broker.html#tag-flow-control)
* [broker-lookup](broker.html#tag-broker-lookup)
* portions of [auth](broker.html#tag-auth)
    * [PUT /auth/credentials](broker.html#operation--auth-credentials-put)
    * [GET /auth/authorities](broker.html#operation--auth-authorities-get)
    * [PUT /auth/changed-credentials](broker.html#operation--auth-changed-credentials-put)
    * [GET /auth/{remuneration-key}/{from-address}/{to-address}/transactions](broker.html#operation--auth--remuneration-key---from-address---to-address--transactions-get)
* [subletting](broker.html#tag-subletting)
* [data](broker.html#tag-data)
* [backchannel queues](broker.html#operation-post-message-WIRE)
* portions of [data-stewardship](broker.html#tag-data-stewardship):
    * [GET /{segment-key}/persistence-status](broker.html#operation---segment-key--persistence-status-get)
    * [PUT /guids](broker.html#operation--guids-put)
    * [GET /guids](broker.html#operation--guids-get)
    * [GET /latest-snapshot-id/{guid}](broker.html#operation--latest-snapshot-id--guid--get)
* portions of [metrics](broker.html#tag-metrics)
    * [WIRE segment-keys](broker.html#operation-segment-keys-WIRE)
# Delegation and Subletting

*Delegation* and *subletting* are two features allowing for services where a service's user isn't forced to participate in [remunerating](https://github.com/overhide/overhide#remuneration-api) the broker. 

Consider the following figure for the rest of this write-up.  

![Given storage.](images/delegate-sublet.svg)

The whole *overhide* broker's data storage contains a slice of storage for a *service-provider* and a separate sliver of storage as a *sublet* with some other user as the *sublet's* *subtenant*.  The *service-provider* owned storage contains a chunk made available as *delegate-data*. 

## Delegate Data

A [susbcriber](identity.md#subscriptions) can specify [segment-keys](glossary.md#segment-key) they own to be accessible by other users--other *subscribers* or [visitors](glossary.md#invitee-visitor-guest)--such that each user has their own *partition* of data under the *segment-key*.

Delegate data stored by a *segment-key* is meant to be accessed by a user who doesn't own the *segment-key*.

Each delegate keeps their data completely separate from other delegates under the same *segment-key*.

The *segment-key* owner controls:

* the names of delegated *segment-keys*
* the full life-cycle of delegated *segment-keys*: when they're migrated, deleted, otherwise made available
* storage allowance
* requisite [fees-schedule](https://overhide.github.io/overhide/docs/broker.html#/definitions/DatastoreKeySettings) to access 

Delegate data is well suited for service (application) specific metadata that needs to be available and independent for each user: the service (application) may not expect the user to furnish such metadata from their own storage.

Delegate data coupled with delegated [backchannel-queues](https://overhide.github.io/overhide/docs/broker.html#tag-backchannel-queues) make available interesting workflows, e.g. user lobbies for an application.

Also of note, having a service (application) depend on delegated [segment-keys](glossary.md#segment-key) restricted by [fees-schedules](https://overhide.github.io/overhide/docs/broker.html#/definitions/DatastoreKeySettings) in critical paths of certain application workflows effectively act as authorization checks for different access tiers into the service.

## Sublet Data

A [susbcriber](identity.md#subscriptions) can specify a portion of their storage quotas for the benefit of a *subtenant*: another user of the broker.

*Subletting* doesn't specify what the data is--unlike *delegation* which specifies particular *segment-keys*.  *Subletting* merely gives a storage allowance to another user.

Unlike *delegation*, the *subtenant* fully owns and controls this *sublet* data.

The *subscriber* providing a *sublet* arrangement only controls whether they will allow users to use some of the *subscriber's* quotas should the meet very specific requirements as outlined in the [PUT /sublet](https://overhide.github.io/overhide/docs/broker.html#operation--sublet-put) API call.

In essence the *subtenant* cannot have or own *overhide* data on this broker or any other broker to be eligible.

The intent is to furnish a user with a sliver of data to continue with a service (application) should they not have any of their own.  It's meant to ease users into the service (application) without a hard requirement for them to come in with their own data storage.

In *sublet* arrangements the *subtenants* are the owners of their own data so as not to diminish any value-propositions of the *overhide* system:  the users own their data--even if subsidized.
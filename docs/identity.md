# Identity

Although *overhide* enables pseudonimity, it absolutely requires the concept of "identity".

Identity is necessary for [authorization](remuneration-api.md) to brokers and preventing abuse.  Brokers do not want to know who's using them:  what service and which user.  But they do need to authorize and possibly get paid for services provided.  To the broker, there is nothing dictating it'll be used for a specific purpose.  A broker might be brought online to support a specific use, but there's nothing preventing anyone from using it for something completely unrelated.  But it doesn't matter at the end of the day: as long as the broker is reimbursed, there is no concept of abuse.

Leveraging [ledgers](remuneration-api.md) for remuneration is key to this reimbursement mechanism.

## Desired Qualities for Identity

It shouldn't be immediately evident which data belongs to which ledger address.

We want the ability to have a public ledger address prove it has [transacted](remuneration-api.md) with the broker's public ledger address in such a way that the [authorized](remuneration-api.md) datastore storage is not traceable to the subscriber.

It'll always be publicly known that a pseudonym subscribes to the broker:  broker's public ledger address is well known and advertised and the pseudonym's transaction is in the public ledger.  What's inhibited is tracability of which data is tied to the pseudonym.

## Identity Tracked by Broker

An *identity* represents a user, a group, or a service provider.  

The broker datastore keeps a mapping of each [datastore-key](glossary.md#datastore-key) to an owning *identity*.

Additionally, the broker datastore keeps a mapping of each [user-address](glossary.md#user-address) to an *identity'*.

Both *identity* and *identity'* are functions of some user provided secret, [secret-phrase](glossary.md#secret-phrase).  *identity* cannot be correlated to *identity'* without at least knowing this [secret-phrase](glossary.md#secret-phrase).

Tracking [datastore-keys](glossary.md#datastore-key) by *identity* allows to prove data ownership.

Tracking [user-address](glossary.md#user-address) by *identity'* ensures only a single [secret-phrase](glossary.md#secret-phrase) is used for a given [user-address](glossary.md#user-address) within the system.  This prevents abuse of the broker:  prevents a user from using multiple *secret-phrases* for a single [user-address](glossary.md#user-address) to double-dip on any paid-for [subscriptions](#subscriptions).

Specifically:

   * *identity* := *user-address<sub>hash</sub>(secret-phrase)* // hash of *secret-phrase* salted with [user-address](glossary.md#user-address)
   * *identity'* := *broker-salt<sub>hash</sub>(secret-phrase)* // hash of *secret-phrase* salted with some *overhide* broker specific salt
   * [datastore-key](glossary.md#datastore-key) maps to *identity* 
   * [user-address](glossary.md#user-address) maps to *identity'*

## Identity Authority

Being authenticated as an "active" *identity* within an *overhide* broker system means ability to create new [datastore-keys](glossary.md#datastore-key) with *identity* as owner, ability to write to such keys, and ability to read any [datastore-key](glossary.md#datastore-key) in the whole system.

Only "active" *identities* are authorized.  An "active" *identity* is an *identity* with an active [subscription](#subscriptions).

Specific read/write usage rates and limits to an *overhide* broker system are also authorized per "active" *identity*:

   * read rate (bytes/second)
   * write rate (bytes/second)
   * storage limit (bytes)

## Identity Authentication

A user authenticates in order to establish their *identity* for a session with an *overhide* broker.

An *identity* is a *user-address<sub>hash</sub>(secret-phrase)* such that *F(secret-phrase, user-address, user-address<sub>sig</sub>(secret-phrase))* returns 'true' where [secret-phrase](glossary.md#secret-phrase), [user-address](glossary.md#user-address), and *user-address<sub>sig</sub>(secret-phrase)* are provided by the authenticated user.  *F(..)* returns 'true' if *secret-phrase* checks out against *user-address<sub>sig</sub>(secret-phrase)*.  

[user-address](glossary.md#user-address) is the public address of a user: likely a ledger public-key.  To prove that the user owns the provided [user-address](glossary.md#user-address), the user [signs](glossary.md#x-keysigmessage) the [secret-phrase](glossary.md#secret-phrase) with [user-address<sub>priv</sub>](glossary.md#x-keypriv): the corresponding ledger [private-key](glossary.md#private-key--secret-key).  

The [secret-phrase](glossary.md#secret-phrase) is something only the authenticated user is privy to.  The *overhide* broker hashes it to create an *identity*.  In the future to prove one's *identity* the user must know the *secret-phrase* such that it hashes to the *identity*, proving the user knows the *secret-phrase* corresponding to said *identity*.

During authentication the broker system also verifies that there is a single mapping from the provided [user-address](glossary.md#user-address) and *secret-phrase* to a *broker-salt<sub>hash</sub>(secret-phrase)*.  If the mapping exists and the computed hash doesn't equal the stored hash, the provided *secret-phrase* is different from one provided earlier.  As such the [user-address](glossary.md#user-address) already has an *identity* in the system with a different *secret-phrase*, and the *secret-phrase* provided cannot be accepted.

If the user remembers the previous *secret-phrase*, the user's original *identity* can be calculated and all [datastore-keys](glossary.md#datastore-key) owned by that *identity* can be changed to be owned by an *identity* with a new *secret-phrase*:  somewhat akin to a password change.  The [user-address](glossary.md#user-address) could be changed as part of this operation as well.

If the user doesn't remember the previous *secret-phrase*, the original *identity* cannot be calculated, all [datastore-keys](glossary.md#datastore-key) owned by that *identity* are unrecoverable.

### Authenticated Sessions

When an *identity* authenticates a token is returned to the client software.  This token is valid for some time and can be used by the client to access *overhide* APIs.  

If the token expires, client software must re-authenticate the *identity*.

The *overhide* broker keeps an in-memory whitelist of valid tokens.  The broker doesn't keep track of mappings between tokens and *identities* or tokens and *user-addresses*.  But the token itself--exchanged between broker and client software--contains the *identity* and a hashed version of the [user-address](glossary.md#user-address).  The hashing approach for the [user-address](glossary.md#user-address) hash is broker specific, but could likely be *broker-salt<sub>hash</sub>(user-address)*.  The token contains sufficient internal checks (hashes) to ensure the whitelisted token is unmodifiable.

Providing a whitelisted token to *overhide* APIs allows them to validate ownership and write/post rights at access time.

## Subscriptions

Subscriptions are either *gratis* or *paid-for* [(time-bound) authorizations to use](remuneration-api.md).  A subscription amount is a tally of all transactions between a subscriber and a broker (or service) on a [supported ledger](https://overhide.github.io/overhide/docs/broker.html#/definitions/RemunerationKey) within a time period: it could be an amount within the last several hours or at least one free transaction at any point in time in history.

*Overhide* isn't concerned with the exact mechanism for subscriptions and recurring subscriptions outside of what's made available with an implementation of the [remuneration API](remuneration-api.md).  There is no *overhide* specific token.  Implementations of the [renumeration API](remuneration-api.md) abstract [different ledgers](https://overhide.github.io/overhide/docs/broker.html#/definitions/RemunerationKey) for subscription purposes.

At a minimum a transaction could take place from a [user-address](glossary.md#user-address) to [broker-address](glossary.md#broker-address) and a simple transaction timestamp check is all that can be done to set subscription expiration dates.  The [remuneration API](remuneration-api.md) abstracting said ledger would make this information available to the *overhide* broker.  The [user-address](glossary.md#user-address)--being a ledger address--is used by the *overhide* broker to check a user's subscription and authorities: access levels to the broker system.  If the transaction shows sufficient fees paid (if any) sufficiently recently (could be eons), the user is subscribed.

More capable ledger implementations could have more advanced smart contracts that make funds available at specific dates (subscription renewal).  Again, these mechanisms and complexities are hidden behind the specific [remuneration API](remuneration-api.md).

Regardless of the technology behind a [remuneration API](remuneration-api.md), the API is there to tell the *overhide* broker if an *identity* is "active" and at what levels.

A subscribed user is a user with an "active" *identity*.  The "active" state of an *identity* is checked explicitly on authentication.  An *overhide* broker tracks two expiration dates for each *identity*:

   1. the *active-until* date: date the *identity* subscription expires.
   1. the *available-until* date: date after which the *overhide* broker may delete all data owned by *identity*.

*active-until* may be same or earlier than *available-until* date.

*active-until* and *available-until* will be updated on authentication as per the most recent ledger transaction from [user-address](glossary.md#user-address) to [broker-address](glossary.md#broker-address).

## *secret-phrase* Security

Note that the *overhide* broker is privy to the user's *secret-phrase* during runtime (not a [secret-key](glossary.md#private-key--secret-key) for a [user-address](glossary.md#user-address)).  As such it's possible the *overhide* broker can be used to expose users' *secret-phrases* such that users' data can be deleted or corrupted: the data cannot be deciphered as the *datastore-value* is (usually) encrypted by [secrets](glossary.md#secrets) known only to the users themselves.  Therefore the *overhide* broker must be trusted to not give up users' *secret-phrases* as they're passed in.  In reality if an *overhide* broker implementation cannot be trusted in this situation, it likely cannot be trusted with it's underlying data storage either.  Such breaches of trust would damage an *overhide* broker's reputation because of the data-loss.

Similarly, an *overhide* broker has no reason to expose *identities*.  These should always be internal to the broker system.

By the same token always ensure the *overhide* broker being accessed has valid and verified SSL certificates.

## Two Address Authentication:  Authorize use of Service

The above "[Subscriptions](#subscriptions)" section describes how an *overhide* broker can verify validity of a user's subscription to use the broker.

The same [mechanism of looking up transactions](remuneration-api.md) from [user-address](glossary.md#user-address) to [broker-address](glossary.md#broker-address) can be used to validate transactions to other public addresses, for example from the [user-address](glossary.md#user-address) to a [service-address](glossary.md#service-address).

Refer to the [broker API](https://overhide.github.io/overhide/docs/broker.html#operation--auth--remuneration-key---from-address---to-address--transactions-get) to this end.

This is just a utility mechanism and doesn't tie into the *identity* and its *active-until* or *available-until* metadata.

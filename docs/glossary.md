# Glossary & Notation

## Canonical -- Concrete Conventions for Concepts

This glossary describes *overhide* concepts in generic terms--but also includes concrete recommendations and specific implementation details forming a canonical set of *overhide* conventions.

A broker could be used with any user-defined algorithms for encryption and any conventions for sourcing secrets; however, for tooling and interoperability it may be beneficial to stick with canonical *overhide*.

Recommendations that constitute canonical *overhide* appear in the following format:

> (canonical)
>
> ... descriptive text of convention ...

Being opinionated does help in building out an ecosystem and standardizing on conventions is important.

The canonical *overhide* is opinionated, and specifies concrete conventions for *overhide* concepts.

## Notation

#### F(X)

Function of *X*:  *X* is used in generation of result from *F*.

#### A &cap; B

*A* is concatenated with *B*.

#### x-key<sub>pub</sub>

*x-key* is used as a public key in a private-public pair.

> (canonical)
>
> Use ECIES with a *secrets[?]* as 32 byte private key: generating 65 byte public key from this private key (see *secrets* below).

#### x-key<sub>priv</sub>

*x-key* is used as a private key in a private-public pair.

> (canonical)
>
> Use ECIES with a *secrets[?]* as this 32 byte private key (see *secrets* below).

#### x-key<sub>pub | priv</sub>&rArr;

This is the "other" key in a private/public key pair.

If *x-key* is a public key, then this is the corresponding private key.  E.g. *some-key<sub>pub</sub>&rArr;* is the private key of *some-key<sub>pub</sub>*.

If *x-key* is a private key, then this is the corresponding public key.  E.g. *some-key<sub>priv</sub>&rArr;* is the public key of *some-key<sub>priv</sub>*.

#### x-key<sub>sym</sub>

*x-key* is used as a key for symmetric encryption/decryption.

In this write-up F(*x-key<sub>sym</sub>*) is context specific:

* *x-key<sub>sym</sub>*(plaintext) means *x-key<sub>sym</sub>* encrypts plaintext into cyphertext.
* *x-key<sub>sym</sub>*(cyphertext) means *x-key<sub>sym</sub>* decrypts cyphertext into plaintext.

> (canonical)
>
> Use AES 256 CTR with a *secrets[?]* as the *secret*  (see *secrets* below).

#### salt<sub>hash</sub>(payload)

Hash of *payload* with provided *salt*.

> (canonical)
>
> Use SHA256 with a *secrets[?]* as salt  (see *secrets* below).

#### x-key<sub>priv | pub | sym</sub>(payload)

Result (ciphertext) of a *payload* encrypted with the specified key.

#### x-key<sub>sig</sub>(message)

*message* is signed with *x-key<sub>priv</sub>*.  *message* and *x-key<sub>sig</sub>(message)* can be verified with *x-key<sub>pub</sub>*.

> (canonical)
>
> Use ECDSA with a *secrets[?]* as 32 byte private key: generating 65 byte public key from this private key (see *secrets* below).

A *signature* is a byte string cryptographically transformed from some *message* by a *private-key* corresponding to a *public-key*.  Another cryptographic transformation can transform the original *message* and the *signature* back into the *public-key*.

To sign:

F(*message*, *private-key*) => *signature*

To proove (recover):

F(*messsage*, *signature*) => *public-key*

Signing is done solely by the *public-key* owner--in private--as the *private-key* must only be known to the owner.  The proof can be done by anyone in possession of the *message* and *signature*: disclosable information.

A signature equates possession of a *private-key* to ownership of a *public-key*.

## Definitions

Definitions below are not in alphabetic order but ordered increasing in specificity to our system.

For example a *member-group-key* is a function of *user-key*: F(user-key). which is an instance of *datastore-key*.  As such these are defined in the order *datastore-key* first, then *user-key*, then *member-group-key*.

> **service, user, group, group-owner, member**
>
> These terms are not part of the *overhide* system, which is really just a datastore with
> secrets, keys, values, and queues.  These terms aid in thinking about use cases.
>
> See the [About](../README.md) section of the *overhide* [README.md](../README.md) for
> context on these terms.
>
> **service** :: software using *overhide* to provide value to user.
> <br/>**user** :: individual entity holding data in the system.
> <br/>**group** :: collection of users sharing data via system.
> <br/>**group-owner** :: user congealing a group together.
> <br/>**member** :: user in context of belonging to a group.

#### broker

The "broker"--abstracted via the broker API--stores and provides users' data.

The broker is a key-value datastore with simplistic CRUD data operations.

The broker doesn't enforce that the keys nor values are encrypted.  This mechanism of *override* is by convention: it's what makes sense.  A user can always verify their data is encrypted on an *overhide* broker system.

Although the broker is a key-value store in the standard sense; the keys have an additional property of being a queue for a backchannel.

#### [overhide.js](overhide.js.md)

Software library, dependency for services communicating to a *broker* and ledgers.  Takes care of establishing identity, hashing and ciphering domain keys and values for *broker* datastore to persist.

Some definitions here pertain to [this](overhide.js.md) specific reference implementation.

#### secret-phrase

User provided phrase to work with a *broker* as an *identity*.  This phrase--when combined with a *user-address*--hashes to an *identity*.

See the [identity write-up](identity.md) and the [secrets write-up](secrets-keybiner.md).

#### identity

The broker datastore keeps an ownership mapping of each *datastore-key* to some user, group, or service provider--some owning *identity*.

A *datastore-key* itself is a function of *identity*:  A *segment-key* for a specific *identity* is a *datastore-key*.  This *identity* usually is--but doesn't have to be--the same value as the "owning" *identity*.

*Identities* are hashes: big values.  There exist special *identities*, values including 0 through 9999, that are restricted from being assigned to actual owners, they are considered "public".  *group-keys* usually end up tied to "public" identities.  Note that *datastore-keys* tied to "public" identities are still owned by someone, the owner's *identity* value is simply not advertised.

See the [identity](identity.md) write-up.

#### user-address

When dealing with *identity*, this is a public address of a user.  Likely a ledger public address (hash):  a user's *ledger<sub>pub</sub>* key.

A user using *overhide* for some service will know their *user-address* as well as be able to prove *identity* using their knowledge of the corresponding private key.  For *ledger<sub>pub</sub>* the user would prove they know their *ledger<sub>priv</sub>*.

#### broker-address

When dealing with *identity*, this is a public address of an *overhide* broker.  Likely a ledger public address (hash).

A recent enough transaction from a *user-address* to a *broker-address* indicates a subscription to the broker's services.

A broker could issue different public addresses for different tiers of service.

#### service-address

When dealing with *identity*, this is a public address of some service provider brokered by *overhide*.  Likely a ledger public address (hash).

A recent enough transaction from a *user-address* to a *service-address* indicates a subscription to the service.

A service provider could issue different public addresses for different tiers of service.

#### private-key / secret-key

The *addresses* above (*user-address*, *broker-address*, *service-address*) are the public keys in [signatures](#x-keysigmessage).

This is the *private-key* in that pair.

A *private-key* is in reference to an *address* and is different from *secrets[?]* and *secret-phrases* as defined herein. 

A *private-key* is always kept private to the user and the client accessing *overhide*.  It is never shared with an *overhide* broker or sent over the wire.  A *private-key* might be asked for in client-side libraries however (e.g. [overhide.js](overhide.js.md)).

See the [secrets write-up](secrets-keybiner.md).

#### secrets

Secrets are byte strings used as keys for cryptographic functions used to encode data and metadata of *datastore-keys*.

Secrets are different than *identity* *secret-phrases*--which are used to authenticate into brokers.  

*Secrets* are used for a myriad of purposes:

* hash salts
* symmetric keys
* asymmetric keys
* signature keys

See the [secrets write-up](secrets-keybiner.md).

> (canonical)
>
> Each *datastore-key* deals with *secrets* from a specific deterministic *secrets* array generated from a *secrets-seed*.
>
> A *secrets* array applies to one or more *datastore-keys*.  An array usually applies to a bunch of related *datastore-keys*: for example all *datastore-keys* from a single application.  A single *secrets* array could easily apply to all of a user's *datastore-keys*.
>
> A *secrets* array is generated from a *secrets-seed*.  Hence all secrets tied to the same *secrets* array are generated from the same *secrets-seed*.
>
> Each *secret* is a 32 byte string: a public addresses from simple ('m/i') [BIP32](http://bip32.org/) key-pair.  Note that the compressed "address" from such a key-pair is 32 bytes after removing the 1 byte BIP32 prefix.
>
> The array of *secrets* are all the public addresses generated as simple ('m/i') [BIP32](http://bip32.org/) key-pairs.
>
> A *secret* at a specific index *i* is denoted by *secrets[i]* and is the simple address at [BIP32](http://bip32.org/) path 'm/i'.  So the 10th secret *secrets[9]* has a [BIP32](http://bip32.org/) path 'm/9'.
>
> The *?* (question mark) index into *secrets*--i.e. *secrets[?]*--denotes any index, any *secret* from the array.
>
> The [BIP32](http://bip32.org/) "master node" for the *secrets* array is seeded with a *secrets-seed* fed into the [BIP39](https://iancoleman.io/bip39/) algorithm.
>
> Some examples:
>
> * *secrets[3]<sub>pub</sub>* -- ECIES public key derived from 32 byte private key that is *secrets[3]*
> * *secrets[6]<sub>sym</sub>* -- *secrets[6]* used as the *secret* in AES 256 encryption

#### wallet

A store of key-pairs to prove identity, work-with transactions, and access funds on a ledger.  

A wallet contains public keys that are the external account addresses (*user-address*, *broker-address*, *service-address*) on a ledger.

It also contains *private-keys* used to sign transactions on behalf of the public keys--accounts.

*overhide* examples are envisioned to coexist with software wallets--something akin to *MetaMask* for Ethereum--which are browser extensions injecting JavaScript (*web3.js*) ledger access to Web applications running with appropriate permissions in said browser.

See the [overhide.js  write-up](overhide.js.md).

#### keybiner

See the [secrets write-up](secrets-keybiner.md).

> (canonical)
>
> A *keybiner* is a collection of related credentials and metadata needed to access a single service or application: crypto key-pairs or *wallet* references, a *secret-phrase*, a *secrets-seed*, metadata for a particular *overhide* broker system and [remuneration provider](remuneration-api.md).  I.e. a *keybiner* is a set of keys for a particular purpose grouped together.

#### domain-key

In a key-value store values are retrieved using human readable keys.  Keys that have some domain specific meaning, prefixed and suffixed in domain meaningful ways.

These domain human readable keys are referred to as *domain-keys* in our write-ups.

> (canonical)
>
> Domain keys are limited to 79 bytes.
>
> The first three bytes conform to the regex `/^[spPtx][1-9]:/`.
>
> As such *domain-keys* are limited to 76 bytes of free-form text.
>
> They should consist of a unique string such as a domain name to avoid conflicts between applications, should the same *secrets[?]* be used for an identical key.
>
> The first three bytes are metadata for the *datastore-value*.  
>
> The first byte is an ASCII character indicating encoding of *datastore-value*:
>
> * 's' -- encrypted with AES 256 CTR symmetric cipher
> * 'p' -- encrypted with ECIES private key
> * 'P' -- encrypted with ECIES public key
> * 'x' -- signature signed with ECDSA private key
> * 't' -- plaintext, no cipher
>
> The second byte is an ASCII character with number of index into *secrets[?]*.  
>
> For example, *domain-key* "P4:acme.com/stuff" indicates that the *datastore-value* for *domain-key* "acme.com/stuff" is encrypted with an ECIES public key corresponding to the 32 byte *secrets[4]* as a private key.
>
> Note that the *domain-key* indicates the *secrets[?]* index and how it's used, but this is all dependant on using the correct *secrets* in the first place; the correct *secrets-seed*.  We likely have the correct *secrets-seed* if the first secret (*secrets[0]*) can decode the first three bytes of a *domain-key* from a *segment-key*; i.e. if we successfully test for the above regex.
>
> See the [secrets write-up](secrets-keybiner.md).

#### segment-key-secret

A key used to salt and encrypt the *segment-key*.

> (canonical)
>
> Key is *secrets[0]*.  It's used as a salt and a symmetric key when producing a *segment-key*.  It is 32 bytes long.

#### segment-key

In *overhide* the *segment-key* is a function of the domain-key limited to 128 bytes.

> (canonical)
>
> A *segment-key* is a 128 bytes long using a 32 byte long *segment-key-secret*.  The *segment-key* is derived by *sement-key-secret<sub>hash</sub>*(*domain-key*)&cap;*segment-key-secret<sub>sym</sub>*(*domain-key'*): a SHA256 hash of the *domain-key*, salted with *segment-key-secret* for the first 32 bytes, followed by 96 bytes of AES256 ciphertext.   The *domain-key'* must necessarily be 79 bytes long, as such *domain-key'* is *domain-key* that's PKCS7 padded.
>
> Note the important property that the *domain-key* is decipherable from the *segment-key*:  *segment-key-secret<sub>sym</sub>* applied against the tail bytes (length - 32 bytes).
>
> To recap:
>
> * *segment-key-secret* := *secrets[0]*
> * *segment-key-secret*.length == 32 bytes
> * *domain-key* := '..' // some text, makes sense in domain
> * *domain-key'* := *domain-key* PKCS7 padded
> * *domain-key'*.length == 79 bytes
> * *segment-key*[0..31] := *sement-key-secret<sub>hash</sub>(domain-key)* // uses SHA256
> * *segment-key*[32..127] := *segment-key-secret<sub>sym</sub>(domain-key')* // uses AES256
> * *domain-key'* == *segment-key-secret<sub>sym</sub>*(*segment-key*[32..127])
> * *domain-key* == *domain-key'* sans padding
> 
> Therefore: 
> 
> * *domain-key* == F(*segment-key*, *segment-key-secret*)
> * *segment-key* == F(*domain-key*, *segment-key-secret*)

#### datastore-key

A key used by *overhide* broker's datastore.

It is a *segment-key* combined with an *identity*.

*segment-keys* are *datastore-keys* segmented by *identity*:  *datastore-keys* with *identity* removed are *segment-keys*.

*Identities* are combined with *segment-keys* to create *datastore-keys* in the form:  *segment-key*@*identity*.

#### datastore-value-secret

A key used to encrypt *datastore-value*.

> (canonical)
>
> See *domain-key* canonical details for *datastore-value-secret* selector.

#### datastore-value

Each datastore-value is usually encrypted.  Details of encryption depend on the client library.  

The encryption key used for the datastore-value is the *datastore-value-secret*.

> (canonical)
>
> See *domain-key* canonical details for key/algorithm selector.

#### publish message vs. write data

A *datastore-key* can have data written to it or have messages published to it.  

Writing data to a *datastore-key* sets a new *datastore-value* for that *datastore-key*.

Publishing a message to a *datastore-key* uses the *datastore-key* as a queue.  It doesn't change the *datastore-value*.  See *backchannel* below.

Consequently the data can be read from a *datastore-key* and the messages can be de-queued.

#### key-owner

Entity that created a certain *datastore-key* and therefore owns that key.

A *key-owner* has a unique *identity* in the system.

The *key-owner* is the only entity that can de-queue messages published to a *datastore-key*, see *backchannel* below.

By default only the *key-owner* can write data to an owned *datastore-key*: notice writing data is different than publishing, publishing uses the *backchannel*, writing simply sets the *datastore-value*.  Other identities can be configured as "writers" against a *datastore-key* by the *key-owner*.

#### backchannel (queue)

Each *datastore-key* can have a message published to it for later de-queueing; beyond just writing and reading data against it.

Messages published to the *datastore-key* are en-queued in a queue.

These queues form a *backchannel* for a particular *datastore-key*.

These messages can be de-queued by the *key-owner*.

A *backchannel*'s publisher can be configured to be "any", "self" or "signed".  A *backchannel* configured for "any" publisher allows anyone to publish a message to it.  A *backchannel* configured with "signed" publishers is configured with a list of public keys allowed to publish a message to the *backchannel*:  these public keys are checked against a publisher's authentication at time of publishing.  By default *backchannels* are restricted to "self":  only the *datastore-key* owner can publish:  *datastore-key* owner can always publish to the *datastore-key's* *backchannel*.

> (canonical)
>
> See *domain-key* canonical details for key/algorithm selector.
>
> All *backchannel* messages are public key encrypted to be private key decrypted by the owner.  The public key is shared out-of-band.

#### user-key

A type of a *datastore-key* with some individual user as the *key-owner*.

#### group-key

A type of a *datastore-key* used to convey a value to a group: some *group-owner* is the *key-owner*.

Since the *datastore-value* needs to be group readable, the *datastore-value-secret* is a public key made available to the group.  Specifically *group-key<sub>pub</sub>* denotes a group-owner's public key used for the *datastore-value-secret*.

#### member-group-key

A *user-key* owned by a *group-owner* but meant for a specific member: established between group-owner and member user to communicate user's view of group data.

> (canonical)
>
> As an example, a *member-group-key* could be established by having a user publish a subscription message to a group's *group-key* along with the user's *secrets[?]<sub>pub</sub>*.  The *group-owner* would use the *secrets[?]<sub>pub</sub>* as the *member-group-key*'s *datastore-value-secret*.  As such the *datastore-value* would be *secrets[?]<sub>pub</sub>* encrypted such that only the user can read it with their *secrets[?]<sub>pub</sub>&rArr;*.  The *segment-key* would be deterministic by both the *group-owner* and member as they both know the *segment-key-secret*.

A member user can usually publish messages to a *member-group-key* but the *datastore-key* is writable only by the *group-owner*: it's a view with feedback.

#### delegate-key

A *datastore-key* owned by a *group-owner* but writable by another user, the "write" delegate.  The idea being that authorities, limits, [remuneration](remuneration-api.md) of the *datastore-key* are the responsibility of the *group-owner*, but data access is by another *identity*.  

The *delegate-key* allows services where the service user isn't forced to participate in [remunerating the broker](remuneration-api.md).

Note that use of a *delegate-key* undermines the user's ownership of their own data.  The benefit is that a service using *overhide* is not forcing the user to participate in owning their data.  The risk is that the user doesn't own their data.

It is recommended delegate data be only used for limited service-specific metadata and to enable collaboration through the service (e.g. lobbies).

Delegate [data access](https://overhide.github.io/overhide/docs/broker.html#tag-delegate) and [backchannel messaging](https://overhide.github.io/overhide/docs/broker.html#tag-backchannel-queues) are made available after a [guest login](https://overhide.github.io/overhide/docs/broker.html#operation--auth-guest-credentials-put).

Read more about *delegation* in the [delegates and subletting write-up](delegation-subletting.md)

Review *delegation* APIs under [delegates](https://overhide.github.io/overhide/docs/broker.html#tag-delegate) and [backchannel-queues](https://overhide.github.io/overhide/docs/broker.html#tag-backchannel-queues) tags.

#### delegate qualification

Data and backchannel message queues can be qualified with a delegate *identity*.  The qualification indicates that the data is actually segmented by delegate.

This means multiple delegates may write into a *datastore-key* and not clobber the other's delegate's data.  The non-delegated data is also left untouched.

Read more about *delegation* in the [delegates and subletting write-up](delegation-subletting.md)

Review *delegation* APIs under [delegates](https://overhide.github.io/overhide/docs/broker.html#tag-delegate) and [backchannel-queues](https://overhide.github.io/overhide/docs/broker.html#tag-backchannel-queues) tags.

#### invitee, visitor, guest

A user must [log in as a visitor](https://overhide.github.io/overhide/docs/broker.html#operation--auth-guest-credentials-put) of a [subscriber](identity.md#subscriptions) to use the *subscriber's* *delegate* data.

*Visitors* may or may not subscribe to the broker instance themselves.  When users login to access another user's *delegate* data they do not [login as subscribers](https://overhide.github.io/overhide/docs/broker.html#operation--auth-credentials-put), they login as [visitors](https://overhide.github.io/overhide/docs/broker.html#operation--auth-guest-credentials-put).  In other words *delegated* data access is only available to *visitors*.  Users who already *subscribe* to the broker cannot access another user's *delegate* data unless they [re-authorize as visitors](https://overhide.github.io/overhide/docs/broker.html#operation--auth-guest-credentials-put).

A *visitor* is always invited to interact with a broker on behalf of the broker's *subscriber*.  

A *visitor* is authenticated with their valid address on an accepted [remuneration ledger](remuneration-api.md).  

A *subscriber* may restrict access to their *segment-keys* pending sufficient transactions from a *visitor's* *user-address* to the *subscriber's* *user-address*.

Read more about *delegation* in the [delegates and subletting write-up](delegation-subletting.md)

Review *delegation* APIs under [delegates](https://overhide.github.io/overhide/docs/broker.html#tag-delegate) and [backchannel-queues](https://overhide.github.io/overhide/docs/broker.html#tag-backchannel-queues) tags.

#### subletting, subtenants

Broker specific allotment of storage *quota* from a [subscriber](identity.md#subscriptions) to a *subtenant* is a *sublet*.

A *subscriber* [remunerates the broker](remuneration-api.md) and maintains storage *quotas* with the broker.  A *sublet* is a subsequent allotment of a fraction of these storage *quotas* to another user:  the *subtenant*.

The *subtenant* has full control and ownership of what's stored in the *sublet* storage.

Think of a *sublet* as merely a broker specific, non-transferable, annotation, to be inquired on the *subtenant's* next authorization into the broker.  

The [PUT /sublet](https://overhide.github.io/overhide/docs/broker.html#operation--sublet-put) API method enumerates constraints on potential *subtenants* in order to leverage *subletting*.

A *subtenant* can access their data as long as the *subscriber* continues to *sublet*, or if the *subtenant* starts [remunerating](remuneration-api.md) the broker and converts to a [subscriber](identity.md#subscriptions) themselves.

Read more about benefits in the [delegates and subletting write-up](delegation-subletting.md)

Review *subletting* APIs under the [subletting](https://overhide.github.io/overhide/docs/broker.html#tag-subletting) tag.

#### data-steward

Implementation dependant--each *overhide* broker is expected to leverage a decentralized persistence network (e.g. [IPFS](https://ipfs.io/)).  The concept of *stewards* is an important aspect of keeping *overhide* [decentralized](decentralization.md).  

Consider a decentralized persistence network with many peers, some of which are *overhide* brokers.  One or more *overhide* brokers that are peers on this network may be subscribed to by a user.  Peers on the network that are subscribed-to *overhide* brokers are "stewards" of a user's data.

An "active" data-steward is an *overhide* broker that the user can write data against.  Read-only data-stewards are "passive".  The active data-steward is the source of truth for the user's most current data.  Passive data-stewards are eventually consistent, pending network delays.  Passive data-stewards necessarily error-out on writes.  Having more than one "active" data-steward may lead to write race-conditions on the distributed persistence network with unknown consequences.

A "pour-active" data-steward is an "active" data-steward with special caveats to allow for consistency during data migration.  Enabling "pour-active" mode on a broker is a temporary measure before transitioning to "active" mode:  read more in the [broker API](https://overhide.github.io/overhide/docs/broker.html#tag-data-stewardship).

It is up to the user--the user's tooling--to manage data stewardship via the [broker API](https://overhide.github.io/overhide/docs/broker.html#tag-data-stewardship).

#### GUID -- Globally Unique ID

The *overhide* system expects brokers to be peers on a distributed persistence network as per the [decentralization write-up](decentralization.md).

Each client to *overhider* brokers can opt-in their data to being stored on this network.  A client's data on this network is referenced using GUIDs -- Globally Unique IDs.  E.g. GUIDs on the [IPFS](https://ipfs.io/) network are IPNS names as discussed in the [decentralization write-up](decentralization.md).

GUIDs are used heavily for [managing data stewardship via the broker APIs](https://overhide.github.io/overhide/docs/broker.html#tag-data-stewardship).

#### working-memory, permanent-memory, shared-memory (persistence-status)

A *datastore-key* may be modified on a broker faster than it can be persisted or shared with other peers on the broker's decentralized persistence network--as is the case with the [IPFS reference implementation](decentralization.md).

Each *datastore-key* has a *persistence-status* indicating resilience of it's most-recent value to catastrophic broker failure.

If the latest value of a *datastore-key* is only in volatile memory (RAM) of the broker, and is not persisted to the broker's non-volatile storage (HDD/NAS), the *datastore-key* is in "working-memory".  The latest value will be returned by this specific *overhide* broker on subsequent reads.  Reads against other passive *data-stewards* will not return the latest value.  The value will be lost if the broker is power-cycled.

If the latest value of a *datastore-key* has been persisted to the broker's non-volatile storage (HDD/NAS), it's considered to be in "permanent-memory".  Reads against other passive *data-stewards* will not return the latest value.  The value will be lost if the broker's permanent storage is corrupted.

If the latest value of a *datastore-key* has been shared to the decentralized persistence network--making it available to other peers--it's considered to be in "shared-memory"--reads against other passive *data-stewards* will return this latest value.  

Use the [GET /{segment-key}/persistence-status](https://overhide.github.io/overhide/docs/broker.html#operation---segment-key--persistence-status-get) API call to determine *persistence-status* of a *segment-key*.

#### broker lookup

Given an *user-address* or a *service-address*, provides ability to lookup that address' *active steward* broker's host name or IP.

Since *user-addresses* and *service-addresses* are ledger specific ([remuneration provider](remuneration-api.md) specific) the *broker lookup* network is specific to a [remuneration-key](https://overhide.github.io/overhide/docs/broker.html#/definitions/RemunerationKey).

See the [broker lookup](lookup.md) write-up.

#### drain-host

A *passive steward*'s host name or IP explicitly set in the *broker lookup* network.

The broker known as a *drain-host* is the original source-of-truth for a user's data during a data migration.

Look at the data-stewardship use cases in [the API](https://overhide.github.io/overhide/docs/broker.html#tag-data-stewardship) and take note that while a *passive steward* is designated as a *drain-host*, the currently *active steward* broker is [put in a temporary "pour" mode](https://overhide.github.io/overhide/docs/broker.html#operation--pour-active-stewardship-put).

[Setting up a drain-host](https://overhide.github.io/overhide/docs/broker.html#operation--broker-lookup-put) and a ["pour" active-steward](https://overhide.github.io/overhide/docs/broker.html#operation--pour-active-stewardship-put) is useful in two migration scenarios:

1. migration to a new *active steward* with the help of a *persistence network*
2. client driven [export](https://overhide.github.io/overhide/docs/broker.html#operation-export-WIRE)+[fill](https://overhide.github.io/overhide/docs/broker.html#operation-fill-WIRE)

See [data-stewardship APIs](https://overhide.github.io/overhide/docs/broker.html#tag-data-stewardship) for use cases.

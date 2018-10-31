# Glossary & Notation

## Notation

#### F(X)

Function of *X*:  *X* is used in generation of result from *F*.

#### A &cap; B

*A* is concatenated with *B*.

#### x-key<sub>pub</sub>

*x-key* is used as a public key in a private-public pair.

#### x-key<sub>priv</sub>

*x-key* is used as a private key in a private-public pair.

#### x-key<sub>pub | priv</sub>&rArr;

This is the "other" key in a private/public key pair.

If *x-key* is a public key, then this is the corresponding private key.  E.g. *some-key<sub>pub</sub>&rArr;* is the private key of *some-key<sub>pub</sub>*.

If *x-key* is a private key, then this is the corresponding public key.  E.g. *some-key<sub>priv</sub>&rArr;* is the public key of *some-key<sub>priv</sub>*.

#### x-key<sub>sym</sub>

*x-key* is used as a key for symmetric encryption/decryption.

In this write-up F(*x-key<sub>sym</sub>*) is context specific:

* *x-key<sub>sym</sub>*(plaintext) means *x-key<sub>sym</sub>* encrypts plaintext into cyphertext.
* *x-key<sub>sym</sub>*(cyphertext) means *x-key<sub>sym</sub>* decrypts cyphertext into plaintext.

#### salt<sub>hash</sub>(payload)

Hash of *payload* with provided *salt*.

#### x-key<sub>priv | pub | sym</sub>(payload)

Result (ciphertext) of a *payload* encrypted with the specified key.

#### x-key<sub>sig</sub>(message)

*message* is signed with *x-key<sub>priv</sub>*.  *message* and *x-key<sub>sig</sub>(message)* can be verified with *x-key<sub>pub</sub>*.

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

#### secrets

Secrets are an array of private key-public address pairs obtained from a 12 word mneumonic using the BIP39 algorithm.

Secrets can be per user, per service, per group, whatever the domain calls for.

*secrets* is an array, *secrets[0]* would be the first key pair of *secrets[0]<sub>pub</sub>* and *secrets[0]<sub>priv</sub>*.  Keys from *secrets* can be used for:

* hash salts
* symmetric keys
* asymmetric keys
* signature keys
* identities
* addresses

*secrets[?]<sub>priv</sub>* would be any/some secret from the array: domain/context specific.

*overhide.js* promotes the use of such secrets via the concept of a "keyring": instead of user generated pass-phrases.

#### wallet

A store of key-pairs to prove identity, work-with transactions, and access funds on a blockchain.  

A wallet contains public keys that are the external account addresses on a blockchain.

It also contains private keys used to sign transactions on behalf of the public keys--accounts.

*overhide* examples are envisioned to coexist with software wallets--something akin to *MetaMask* for Ethereum--which are browser extensions injecting JavaScript (*web3.js*) blockchain access to Web applications running with appropriate permissions in said browser.

#### keybiner & keyrings

A *keybiner* is a collection of *keyrings*.  A *keyring* is a mapping of assymetric key-pairs to a particular *overhide* broker system, for a particular *service*.  I.e. a *keyring* is a set of keys for a particular purpose grouped together.

An application will repetativelly access a particular *overhide* broker instance with a particular blockchain public key (address) for identification.  The corresponding blockchain private key is also needed on-hand--only in-browser--for signatures.  For convenience, better user experience, better application flow, it's desirable to store a mapping of all these identifiers and tokens on the client machine, e.g. in the client browser.  All these identifiers and tokens for a single purpose comprise a *keyring*.  Multiple such *keyrings* are stored in the client's *keybiner*.

The assymetric key-pairs in *keyrings* include public blockchain address keys and references to their private signing keys, as well as any additional keys the application may want to keep track of.  The additional keys are described above in the *secrets* section, and are usually generated using a mnemonic.

It's unlikely the private signing keys of a public blockchain will be made available to the *keybiner*, they will be kept in the user's *wallets*.  As such the *keyrings* may not store the keys themselves, but references to the keys in their particular *wallet*.

In terms of Web applications in a browser--a *keybiner* is a browser extension that uses *localstorage* to store *keyrings* and injects a JavaScript library to make the *keyrings* accessible to said Web applications; provided they're running with appropriate permissions in said browser.  The injected *keybiner* JavaScript library is *overhide.js*.

Considering a Web application will use a *keybiner* injected *overhide.js* to encrypt *keyrings* in the browser's *localstorage*--these *keyrings* are tied to a particular application--and for demonstration purposes may look like the following:

```
  'keybiner': ['keyring1':{
                  <tag>: {                  
                    'brokerUrl':'..',
                    'keys': [
                      {'privateOrRef':'..',
                      'public':'..'
                      },
                      ...
                    ],
                    'mnemonic':'..',
                    'format':'..'
                  },
                  <tag>: {..}
               },
               'keyring2':{..},
               ...
               'keyringN':{..}]
```

Each *keyring* has an alias; "keyring1", "keyring2", "keyringN".  These can be thought of as login names in traditional login schemes.  The *overhide.js* library exposed by *keybiner* provides utilities to symmetrically encrypt each *keyring*.  When a user provides the appropriate passphrase to decrypt a keyring (a login), access to the broker mapping and all the stored keys is granted.  Hence the alias and the symmetric passphrase are the access credentials--and they're never sent over the wire.  From this point forward the application has all the information it needs to synchronize with the specified *overhide* instance ('brokerUrl').  The application can access tagged sets of keys.  The keys may or may not be for a broker--the 'brokerUrl' is optional--the 'format' dictates implementation specific metadata about the keys.  

Each *keyring* can be exported or re-setup from scratch--it's a convenience store to aid end-user experience.  An end-user can export their *keyring* as an encrypted payload, transfer to another *keybiner* running in another browser, import, provide passphrase, done.  To setup a new *keyring* from scratch--let's say in a new browser on a new computer--the end-user needs to remember their *overhide* broker system ('brokerUrl', likely dictated by the app, a UI dropdown), blockchain keys (likely using a *wallet*), and optionally a mnemonic for other *secrets*--if additional secrets are required by the app.

#### broker

The "broker"--abstracted via the broker API--stores and provides users' data.

The broker is a key-value datastore with simplistic CRUD data operations.

The broker doesn't enforce that the keys nor values are encrypted.  This mechanism of *override* is by convention: it's what makes sense.  A user can always verify their data is encrypted on an *overhide* broker system.

Although the broker is a key-value store in the standard sense; the keys have an additional property of being a queue for a backchannel.

#### [overhide.js](overhide.js.md)

Software library, dependency for services communicating to a *broker* and ledgers.  Takes care of establishing identity, hashing and ciphering domain keys and values for *broker* datastore to persist.

Some definitions here pertain to [this](overhide.js.md) specific reference implementation.

#### domain-key

In a key-value store values are retrieved using human readable keys.  Keys that have some domain specific meaning, prefixed and suffixed in domain meaningful ways.

These domain human readable keys are referred to as *domain-keys* in our write-ups.

#### segment-key-secret

A key used to salt and encrypt the *segment-key*.

In *overhide.js* this is either the private key or public address from one of the *secrets*: *secrets[?]<sub>pub|priv</sub>*.  It's used as a salt and a symmetric key when producing a *segment-key*.  As such in *overhide.js* a *segment-key-secret* is necessarily 32 bytes long.  If a byte stream longer than 32 bytes is used as a *segment-key-secret* it is truncated to the last 32 bytes.

#### segment-key

In *overhide* the *segment-key* is a function of the domain-key limited to 128 bytes.

In *overhide.js* a *segment-key* is a 128 bytes long using a 32 byte long *segment-key-secret*.  The *segment-key* is derived by *sement-key-secret<sub>hash</sub>*(*domain-key*)&cap;*segment-key-secret<sub>sym</sub>*(*domain-key'*): a SHA256 hash of the *domain-key*, salted with *segment-key-secret* for the first 32 bytes, followed by 96 bytes of AES256 ciphertext.   The *domain-key'* must necessarily be 79 bytes long, as such *domain-key'* is *domain-key* that's PKCS7 padded.

Note the important property that the *domain-key* is decipherable from the *segment-key*:  *segment-key-secret<sub>sym</sub>* applied against the tail bytes (length - 32 bytes).

To recap, in *overhide.js*:

* *segment-key-secret* := *secrets[?]<sub>pub|priv</sub>*
* *segment-key-secret*.length == 32 bytes
* *domain-key* := '..' // some text, makes sense in domain
* *domain-key'* := *domain-key* PKCS7 padded
* *domain-key'*.length == 79 bytes
* *segment-key*[0..31] := *sement-key-secret<sub>hash</sub>(domain-key)* // uses SHA256
* *segment-key*[32..127] := *segment-key-secret<sub>sym</sub>(domain-key')* // uses AES256
* *domain-key'* == *segment-key-secret<sub>sym</sub>*(*segment-key*[32..127])
* *domain-key* == *domain-key'* sans padding

Therefore: 

* *domain-key* == F(*segment-key*, *segment-key-secret*)
* *segment-key* == F(*domain-key*, *segment-key-secret*)

#### datastore-key

A key used by *overhide* broker's datastore.

It is a *segment-key* combined with an *identity*.

*segment-keys* are *datastore-keys* segmented by *identity*:  *datastore-keys* with *identity* removed are *segment-keys*.

*Identities* are combined with *segment-keys* to create *datastore-keys* in the form:  *segment-key*@*identity*.

#### identity

The broker datastore keeps an ownership mapping of each *datastore-key* to some user, group, or service provider--some owning *identity*.

A *datastore-key* itself is a function of *identity*, which usually is the same value as the owning *identity*.

*Identities* are hashes: big values.  There exist special *identities*, values including 0 through 9999, that are restricted from being assigned to actual owners, they are considered "public".  *group-keys* usually end up tied to "public" identities.  Note that *datastore-keys* tied to "public" identities are still owned by someone, the owner's *identity* value is simply not advertised.

See the [identity](identity.md) write-up.

#### datastore-value-secret

A key used to encrypt *datastore-value*.

In *overhide.js* this is either the private key or public address from one of the *secrets* used as a key when encrypting a *datastore-value*.  As such in *overhide.js* a *datastore-value-secret* is necessarily 32 bytes long.  If a byte stream longer than 32 bytes is used as a *datastore-value-secret* it is truncated to the last 32 bytes.

The *segment-key-secret* can usually be the *datastore-value-secret*.

#### datastore-value

Each datastore-value is somehow encrypted.  Details of encryption depend on the client library.  

In *overhide.js* a *datastore-value* can be encrypted using:

* AES256 for symmetric encryption
* ECDSA for asymmetric encryption

The encryption key used for the datastore-value is the *datastore-value-secret*.

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

In *overhide.js* all *backchannel* messages are public key encrypted to be private key decrypted by the owner.

#### user-key

A type of a *datastore-key* with some individual user as the *key-owner*.

In *overhide.js* it's a function of a *domain-key* and a private key from one of the user's secrets; the private key is used as the *segment-key-secret*.  The *datastore-value* is AES256 encrypted.

#### group-key

A type of a *datastore-key* used to convey a value to a group: some *group-owner* is the *key-owner*.

Since the *datastore-value* needs to be group readable, the *datastore-value-secret* is a public key made available to the group.  Specifically *group-key<sub>pub</sub>* denotes a group-owner's public key used for the *datastore-value-secret*.

In *overhide.js* the *datastore-value* is ECDSA (public key) encrypted with a *datastore-value-secret* that's shared with the group.  This is one of the *secrets[?]<sub>pub</sub>* keys.  The same key is used for the *segment-key-secret* to salt and encrypt the *segment-key*.

#### member-group-key

A *user-key* owned by a *group-owner* but meant for a specific member: established between group-owner and member user to communicate user's view of group data.

As an example, a *member-group-key* could be established by having a user publish a subscription message to a group's *group-key* along with the user's *secrets[?]<sub>pub</sub>*.  The *group-owner* would use the *secrets[?]<sub>pub</sub>* as the *member-group-key*'s *segment-key-secret* and *datastore-value-secret*.  As such the *datastore-value* would be *secrets[?]<sub>pub</sub>* encrypted such that only the user can read it with their *secrets[?]<sub>pub</sub>&rArr;*.  The *segment-key* would be deterministic by both the *group-owner* and member as they both know the *segment-key-secret*.

A member user can usually publish messages to a *member-group-key* but the *datastore-key* is writable only by the *group-owner*: it's a view with feedback.

#### delegate-key

A *datastore-key* owned by a *group-owner* but writable by another user, the "write" delegate.  The idea being that authorities, limits, remuneration of the *datastore-key* are the responsibility of the *group-owner*, but data stored is by another *identity*.  

The *delegate-key* allows services where the service user isn't forced to participate in remunerating the broker.

Note that use of a *delegate-key* undermines the user's ownership of their own data.  The benefit is that a service using *overhide* is not forcing the user to participate in owning their data.  The risk is that the user doesn't own their data.

#### delegate qualification

Data and backchannel message queues can be qualified with a delegate *identity*.  The qualification indicates that the data is actually segmented by delegate.

This means multiple delegates my write into a *datastore-key* and not clobber the other's delegate's data.  The non-delegated data is also left untouched.

#### user-address

When dealing with *identity*, this is a public address of a user.  Likely a blockchain public address (hash):  a user's *blockchain<sub>pub</sub>* key.

A user using *overhide* for some service will know their *user-address* as well as be able to prove *identity* using their knowledge of the corresponding private key.  For *blockchain<sub>pub</sub>* the user would prove they know their *blockchain<sub>priv</sub>*.

#### broker-address

When dealing with *identity*, this is a public address of an *overhide* broker.  Likely a blockchain public address (hash).

A recent enough transaction from a *user-address* to a *broker-address* indicates a subscription to the broker's services.

A broker could issue different public addresses for different tiers of service.

#### service-address

When dealing with *identity*, this is a public address of some service provider brokered by *overhide*.  Likely a blockchain public address (hash).

A recent enough transaction from a *user-address* to a *service-address* indicates a subscription to the service.

A service provider could issue different public addresses for different tiers of service.

#### invitee

A user who does not subscribe to the broker instance but likely subscribes to a service furnishing data on the broker instance.

An invitee is always invited to interact with a broker on behalf of an actual broker subscriber.  The service does pay subscription fees to the broker.

An invitee is authenticated as owning a valid address on an accepted remuneration provider.  

The service may allow some *datastore-keys* to be written to or published to by the invitee.  The service may restrict access to its *segment-keys* pending sufficient transactions from the invitee address to the service address.

#### data-steward

Implementation dependant--each *overhide* broker is expected to leverage a decentralized persistence network (e.g. [IPFS](https://ipfs.io/)).  The concept of *stewards* is an important aspect of keeping *overhide* [decentralized](decentralization.html).  

Consider a decentralized persistence network with many peers, some of which are *overhide* brokers.  One or more *overhide* brokers that are peers on this network may be subscribed to by a user.  Peers on the network that are subscribed-to *overhide* brokers are "stewards" of a user's data.

An "active" data-steward is an *overhide* broker that the user can write data against.  Read-only data-stewards are "passive".  The active data-steward is the source of truth for the user's most current data.  Passive data-stewards are eventually consistent, pending network delays.  Passive data-stewards necessarily error-out on writes.  Having more than one "active" data-steward may lead to write race-conditions on the distributed persistence network with unknown consequences.

It is up to the user--the user's tooling--to manage data stewardship via the [broker API](broker.html#tag-data-stewardship).

#### GUID -- Globally Unique ID

The *overhide* system expects brokers to be peers on a distributed persistence network as per the [decentralization write-up](decentralization.md).

Each client to *overhider* brokers has their data shared on this network.  A client's data on this network is referenced using GUIDs -- Globally Unique IDs.  E.g. GUIDs on the [IPFS](https://ipfs.io/) network are IPNS names as discussed in the [decentralization write-up](decentralization.md).

GUIDs are used heavily for [managing data stewardship via the broker APIs](broker.html#tag-data-stewardship).

#### working-memory, permanent-memory, shared-memory (persistence-status)

A *datastore-key* may be modified on a broker faster than it can be persisted or shared with other peers on the broker's decentralized persistence network--as is the case with the [IPFS reference implementation](decentralization.html).

Each *datastore-key* has a *persistence-status* indicating resilience of it's most-recent value to catastrophic broker failure.

If the latest value of a *datastore-key* is only in volatile memory (RAM) of the broker, and is not persisted to the broker's non-volatile storage (HDD/NAS), the *datastore-key* is in "working-memory".  The latest value will be returned by this specific *overhide* broker on subsequent reads.  Reads against other passive *data-stewards* will not return the latest value.  The value will be lost if the broker is power-cycled.

If the latest value of a *datastore-key* has been persisted to the broker's non-volatile storage (HDD/NAS), it's considered to be in "permanent-memory".  Reads against other passive *data-stewards* will not return the latest value.  The value will be lost if the broker's permanent storage is corrupted.

If the latest value of a *datastore-key* has been shared to the decentralized persistence network--making it available to other peers--it's considered to be in "shared-memory"--reads against other passive *data-stewards* will return this latest value.  

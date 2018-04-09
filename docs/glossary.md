# Glossary & Notation

## Notation

#### F(X)

Function of *X*:  *X* is used in generation of result from *F*.

#### A ◠ B

*A* is concatenated with *B*.

#### x-key<sub>pub</sub>

*x-key* is used as a public key in a private-public pair.

#### x-key<sub>priv</sub>

*x-key* is used as a private key in a private-public pair.

#### x-key<sub>pub | priv</sub>⇒

*y-key* is the "other" key in a private/public key pair.

If *x-key* is a public key, then *y-key* is the corresponding private key.  E.g. *some-key<sub>pub</sub>⇒* is the private key of *some-key<sub>pub</sub>*.

If *x-key* is a private key, then *y-key* is the corresponding public key.  E.g. *some-key<sub>priv</sub>⇒* is the public key of *some-key<sub>priv</sub>*.

#### x-key<sub>sym</sub>

*x-key* is used as a key for symmetric encryption/decryption.

#### x-key<sub>priv | pub | sym</sub>(payload)

*payload* is encrypted with the specified key.

#### message<sub>sig(x-key)</sub>

*message* is signed with *x-key<sub>priv</sub>*.  *message* and *message<sub>sig(x-key)</sub>* can be verified with *x-key<sub>pub</sub>*.

#### payload<sub>hash</sub>

Hash of *payload*.

## Definitions

Definitions below are not in alphabetic order but ordered increasing in specificity to our system.  

For example a *member-channel* is a function of *user-key*: F(user-key). which is an instance of *datastore-key*.  As such these are defined in the order *datastore-key* first, then *user-key*, then *member-channel*.

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

Secrets are an array of private-public key pairs obtained from a 12 word mneumonic using the BIP39 algorithm.

Secrets can be per user, per service, per group, whatever the domain calls for.

*secrets* is an array, *secrets[0]* would be the first key pair of *secrets[0]<sub>pub</sub>* and *secrets[0]<sub>priv</sub>*.  Keys from *secrets* can be used for anything in the system.

*secrets[?]<sub>priv</sub>* would be any/some secret from the array: domain/context specific.

#### broker

The "broker"--abstracted via the broker API--uses secrets to store and provide users' data.

The broker is a key-value datastore with simplistic CRUD data operations.

The broker doesn't enforce that the keys nor values are encrypted.  This mechanism of *override* is by convention: it's what makes sense.

The keys have an additional property of being a queue as a backchannel.

#### domain-key

In a key-value store values are retrieved using human readable keys.  Keys that have some domain specific meaning, prefixed and suffixed in domain meaningful ways.

These domain human readable keys are referred to as *domain-keys* in our writeups.

#### datastore-key

*F(domain-key)<sub>hash</sub>*

In *overhide* the *datastore-key* is a non human-readable hash that is some function of the domain-key.

Each datastore-key is 64 bytes long.

Each datastore-key is expected to be 2 SHA256 hashes.  The idea being that a concatenation of 2 hashes reduces already miniscule chance of conflict.

#### datastore-value

Each datastore-value is somehow encrypted.  Details of encryption depend on service needs and service's internal contract.  E.g. *user-secret* encrypted for *user-key*, *private-key* encrypted with *group-secret* for *group-key*.

#### key-owner

Entity that created a certain *datastore-key* and therefore owns that key.

The *key-owner* is the only entity that can read data posted to a *datastore-key*, see *backchannel* below.

#### backchannel (queue)

Each *datastore-key* can have a message posted to it; beyond just reading a from it.

Messages posted to the *datastore-key* are enqueued in a queue.

These queues form a *backchannel* for a particular *datastore-key*.

These messages can be dequeued by the *key-owner*.

#### user-key

A type of a *datastore-key* with some individual user as the *key-owner*.

A *datastore-key* (64 bytes/2 hashes) with the first SHA256 hash (32 bytes) being concatenation of *user-key* and some domain specific key:  *(user-key◠key)<sub>hash</sub>*.  The second SHA256 hash (32 bytes) being some other key *key'<sub>hash</sub>* where *key'* is either a different domain specific key or same key from the first hash.

#### group-key

A type of a *datastore-key* used to convey a value to a group: some *group-owner* is the *key-owner*.

A *datastore-key* (64 bytes/2 hashes) with the first SHA256 hash (32 bytes) being some public-key generated by *group-owner*.  The second SHA256 hash (32 bytes) can be null, all zeroes, 0x0000..0000.

#### psa-group-key

A *group-key* used to convey public information from the *group-owner*: value is *group-key<sub>pub</sub>* decryptable; encrypted using *group-key<sub>pub</sub>⇒ by *group-owner*.  Since the *group-key* decryptable value is public, it's considered a PSA: the public at large can read it.

 Backchannel--if enabled by *group-owner*--is to enqueue messages from anyone (public) by encrypting with *group-key<sub>pub</sub>*.  Decryptable by *group-owner* with the *group-key<sub>pub</sub>⇒*

#### members-group-key

A *group-key* used to convey information to all members of a group: value is decryptable by members only--*group-key<sub>pub</sub>⇒* or *k<sub>sym</sub>* encrypted by *group-owner*.  Decryption key (either *group-key<sub>pub</sub>* or *k<sub>sym</sub>*) disseminated via each member's *member-channel*, for member eyes only.

Backchannel--if enabled by *group-owner*--is to enqueue messages from anyone (public) by encrypting with *group-key<sub>pub</sub>* or *k<sub>sym</sub>*.  Decryptable by *group-owner* with the *group-key<sub>pub</sub>⇒* or *k<sub>sym</sub>*.

#### member-group-key

A *user-key* owned by a *group-owner* but meant for a specific member: established between group-owner and member user to communicate user's view of group data.

As an example, a *member-group-key* could be established by having a user post a subscription message to the *group-key* along with the user's *secrets[?]<sub>pub</sub>*.  The *group-owner* would use *(secrets[?]<sub>pub</sub>◠key)<sub>hash</sub>* for the first SHA256 of a *member-group-key*, *key<sub>hash</sub>* for the second SHA256, and start setting a value at that *datastore-key*.  The value would be *secrets[?]<sub>pub</sub>* encrypted such that only the user can read it with their *secrets[?]<sub>pub</sub>⇒*.  

#### identity

The broker datastore keeps a mapping of each *datastore-key* to some user, group, or service provider.  This is known as an *identity*.

See the [identity](identity.md) write-up.

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

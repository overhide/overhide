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

#### salt<sub>hash</sub>(payload)

Hash of *payload* with provided *salt*.

#### x-key<sub>priv | pub | sym</sub>(payload)

Result (ciphertext) of a *payload* encrypted with the specified key.

#### x-key<sub>sig</sub>(message)

*message* is signed with *x-key<sub>priv</sub>*.  *message* and *x-key<sub>sig</sub>(message)* can be verified with *x-key<sub>pub</sub>*.

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

The broker doesn't enforce that the keys nor values are encrypted.  This mechanism of *override* is by convention: it's what makes sense.  A user can always verify their data is encrypted on an *overhide* broker instance.

The keys have an additional property of being a queue as a backchannel.

#### client library (wallet)

Software library, dependency for services communicating to a *broker*.  Takes care of establishing identity, hashing and ciphering domain keys and values for *broker* datastore to persist.

#### domain-key

In a key-value store values are retrieved using human readable keys.  Keys that have some domain specific meaning, prefixed and suffixed in domain meaningful ways.

These domain human readable keys are referred to as *domain-keys* in our writeups.

#### crypto-key

Some form of a security token; salt, public key, private key, symmetric key, whatever makes sense in a given context.

#### segment-key

In *overhide* the *segment-key* is a function of the domain-key.

Each *segment-key* is 64 bytes long.

In the reference implementation of the *client library* a *segment-key* is a *crypto-key<sub>hash</sub>(domain-key)◠crypto-key<sub>sym</sub>(domain-key)*: a hash for the first 32 bytes (SHA256) followed by a 32 byte ciphertext (2 AES blocks), both using the same *crypto-key*.  The idea being that a concatenation of these two reduces already miniscule chance of conflict.

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

#### datastore-value

Each datastore-value is somehow encrypted.  Details of encryption depend on the client library (wallet).  

In reference implementation of the *client library* a *datastore-value* is a *crypto-key<sub>sym</sub>(payload)*: a payload value AES-encrypted with some *crypto-key*.

#### key-owner

Entity that created a certain *datastore-key* and therefore owns that key.

A *key-owner* has a unique *identity* in the system.

The *key-owner* is the only entity that can read data posted to a *datastore-key*, see *backchannel* below.

#### backchannel (queue)

Each *datastore-key* can have a message posted to it; beyond just reading a from it.

Messages posted to the *datastore-key* are enqueued in a queue.

These queues form a *backchannel* for a particular *datastore-key*.

These messages can be dequeued by the *key-owner*.

#### user-key

A type of a *datastore-key* with some individual user as the *key-owner*.

In the reference implementation of the *client library* it's a function of a *domain-key* and a private key from one of the user's secrets; the private key is used as the *crypto-key* to salt and encrypt the *domain-key*.

#### group-key

A type of a *datastore-key* used to convey a value to a group: some *group-owner* is the *key-owner*.

In the reference implementation of the *client library* it's a function of a *domain-key* and a public key from the *group-owner* and shared with the group; the public key is used as the *crypto-key* to salt and encrypt the *domain-key*.

#### psa-group-key

A *group-key* used to convey public information from the *group-owner*: value is *group-key<sub>pub</sub>* decryptable; the *datastore-key*'s *crypto-key* is *group-key<sub>pub</sub>⇒* and encrypted by *group-owner*.  Since the *group-key* decryptable value is public, it's considered a PSA: the public at large can read it.

 Backchannel--if enabled by *group-owner*--is to enqueue messages from anyone (public) by encrypting with *group-key<sub>pub</sub>*.  Decryptable by *group-owner* with the *group-key<sub>pub</sub>⇒*

#### members-group-key

A *group-key* used to convey information to all members of a group: value is decryptable by members only--*group-key<sub>pub</sub>⇒* or *k<sub>sym</sub>* encrypted by *group-owner*.  Decryption key (either *group-key<sub>pub</sub>* or *k<sub>sym</sub>*) disseminated via each member's *member-channel*, for member eyes only.

Backchannel--if enabled by *group-owner*--is to enqueue messages from anyone (public) by encrypting with *group-key<sub>pub</sub>* or *k<sub>sym</sub>*.  Decryptable by *group-owner* with the *group-key<sub>pub</sub>⇒* or *k<sub>sym</sub>*.

#### member-group-key

A *user-key* owned by a *group-owner* but meant for a specific member: established between group-owner and member user to communicate user's view of group data.

As an example, a *member-group-key* could be established by having a user post a subscription message to a group's *group-key* along with the user's *secrets[?]<sub>pub</sub>*.  The *group-owner* would use the *secrets[?]<sub>pub</sub>* as the *member-group-key*'s *crypto-key*.  As such the value would be *secrets[?]<sub>pub</sub>* encrypted such that only the user can read it with their *secrets[?]<sub>pub</sub>⇒*.  

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

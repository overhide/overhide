Although *overhide* enables pseudonimity, it absolutely requires the concept of "identity".

Identity is necessary for remuneration to brokers and preventing abuse.  Brokers do not want to know who's using them:  what service and which user.  But they do need to get reimbursed for services provided.  To the broker, there is nothing dictating it'll be used for a specific purpose.  A broker might be brought online to support a specific use, but there's nothing preventing anyone from using it for something completely unrelated.  But it doesn't matter at the end of the day: as long as the broker is reimbursed, there is no concept of abuse.

Leveraging blockchains for remuneration is key to this reimbursement mechanism.

## Desired Qualities for Identity

It shouldn't be immediately evident which blockchain address pays for what data.

We want the ability to have a public blockchain address prove it has paid a "subscription" to the broker's public blockchain address in such a way that the paid for datastore storage is not traceable to the subscriber.

It'll always be publicly known that a pseudonym subscribes to the broker:  broker's public blockchain address is well known and advertised and the pseudonym's payment is in the public ledger.  What's inhibited is tracability of which data is tied to the pseudonym.

An extension of this quality is a desire for a subscriber's blockchain address to pay for different sets of datastore storage--different services, apps--without anyone but the subscriber being able to correlate the sets to their blockchain pseudonym.

## Identity Tracked by Broker

The broker datastore keeps a mapping of each *datastore-key* to an *identity*.

The *identity* could represent a user, a group, or a service provider.

## Identity Authority

Being authenticated as some *identity* within an *overhide* broker system means ability to create new *datastore-keys* with *identity* as owner, ability to write to such keys, and ability to read any *datastore-key* in the whole system.

Read/write usage rates and limits for an *overhide* broker system are also authorized via the *identity*:

* read rate (bytes/second)
* read limit (bytes)
* write rate (bytes/second)
* write limit (bytes)

## Identity Authentication

A user authenticates in order to establish their *identity* for a session with an *overhide* broker.

An *identity* is a *secret-phrase<sub>hash</sub>* such that *F(secret-phrase, user-address, secret-phrase<sub>sig(user-address)</sub>)* returns 'true' where *secret-phrase*, *user-address*, and *secret-phrase<sub>sig(user-address)</sub>* are provided by the authenticated user.  *F(..)* returns 'true' if *secret-phrase* checks out against *secret-phrase<sub>sig(user-address)</sub>*.  

*user-address* is the public address of a user: likely a blockchain public-key.  To prove that the user owns the provided *user-address*, the user signs the *secret-phrase* with *user-address<sub>priv</sub>*: the corresponding blockchain private-key.  

The *secret-phrase* is something only the authenticated user is privy to.  The *overhide* broker hashes it to create an *identity*.  In the future to prove one's *identity* the user must know the *secret-phrase* such that it hashes to the *identity*, proving the user knows the *secret-phrase* corresponding to said *identity*.

#### Subscriptions

The *user-address*--being a blockchain address--is used by the *overhide* broker to check a user's subscription and authorities: access levels to the broker system.  An *overhide* broker can check the blockchain for a transaction from the *user-address* to the broker's *broker-address*.  If the transaction shows sufficient fees paid sufficiently recently (timestamp check), the user is subscribed.

#### *secret-phrase* Security

Note that the *overhide* broker is privy to the user's *secret-phrase* during runtime.  As such it's possible the *overhide* broker can be used to expose users' *secret-phrases* such that users' data can be deleted or corrupted: the data cannot be deciphered as the *datastore-value* is encrypted by keys known only to the users themselves.  Therefore the *overhide* broker must be trusted to not give up users' *secret-phrases* as they're passed in.  In reality if an *overhide* broker implementation cannot be trusted in this situation, it likely cannot be trusted with it's underlying data storage either.  Such breaches of trust would damage an *overhide* broker's reputation because of the data-loss.

An *overhide* broker has no reason to expose *identities*.  These should always be internal to the broker system.

## Two Address Authentication:  Authorize use of Service

The above "Subscriptions" section describes how an *overhide* broker can verify validity of a user's subscription to use the broker.

The same mechanism of looking up transactions from *user-address* to *broker-address* can be used to validate transactions to other public addresses, for example from the *user-address* to a *service-address*.

Although *overhide* enables pseudonimity, it absolutely requires the concept of "identity".

Identity is necessary for remuneration to brokers and preventing abuse.  Brokers do not want to know who's using them:  what service and which user.  But they do need to get reimbursed for services provided.  To the broker, there is nothing dictating it'll be used for a specific purpose.  A broker might be brought online to support a specific use, but there's nothing preventing anyone from using it for something completely unrelated.  But it doesn't matter at the end of the day: as long as the broker is reimbursed, there is no concept of abuse.

Leveraging blockchains for remuneration is key to this reimbursement mechanism.

## Desired Qualities for Identity

It shouldn't be immediately evident which blockchain address pays for what data.

We want the ability to have a public blockchain address prove it has paid a "subscription" to the broker's public blockchain address in such a way that the paid for datastore storage is not traceable to the subscriber.

It'll always be publicly known that a pseudonym subscribes to the broker:  broker's public blockchain address is well known and advertised and the pseudonym's payment is in the public ledger.  What's inhibited is tracability of which data is tied to the pseudonym.

## Identity Tracked by Broker

An *identity* represent a user, a group, or a service provider.  

The broker datastore keeps a mapping of each *datastore-key* to an *identity*.

Additionally, the broker datastore keeps a mapping of each *user-address* to an *identity'*.

Both *identity* and *identity'* are functions of some user provided secret, *secret-phrase*.  *identity* cannot be correlated to *identity'* without at least knowing this *secret-phrase*.

Tracking *datastore-keys* by *identity* allows to prove data ownership.

Tracking *user-address* by *identity'* ensures only a single *secret-phrase* is used for a given *user-address* within the system.  This enables fair remuneration to the broker:  prevents a user from using multiple *secret-phrases* for a single *user-address* to double-dip on a paid-for subscription.

Specifically:

   * *datastore-key* maps to *user-address<sub>hash</sub>(secret-phrase)*: *identity* is a hash of *secret-phrase* salted with *user-address*.
   * *user-address* maps to *broker-salt<sub>hash</sub>(secret-phrase)*: *identity'* is a hash of *secret-phrase* salted with some *overhide* broker specific salt.

## Identity Authority

Being authenticated as an "active" *identity* within an *overhide* broker system means ability to create new *datastore-keys* with *identity* as owner, ability to write to such keys, and ability to read any *datastore-key* in the whole system.

Only "active" *identities* are authorized.  An "active" *identity* is an *identity* with an active subscription.

Specific read/write usage rates and limits to an *overhide* broker system are also authorized per "active" *identity*:

   * read rate (bytes/second)
   * write rate (bytes/second)
   * storage limit (bytes)

## Identity Authentication

A user authenticates in order to establish their *identity* for a session with an *overhide* broker.

An *identity* is a *user-address<sub>hash</sub>(secret-phrase)* such that *F(secret-phrase, user-address, user-address<sub>sig</sub>(secret-phrase))* returns 'true' where *secret-phrase*, *user-address*, and *user-address<sub>sig</sub>(secret-phrase)* are provided by the authenticated user.  *F(..)* returns 'true' if *secret-phrase* checks out against *user-address<sub>sig</sub>(secret-phrase)*.  

*user-address* is the public address of a user: likely a blockchain public-key.  To prove that the user owns the provided *user-address*, the user signs the *secret-phrase* with *user-address<sub>priv</sub>*: the corresponding blockchain private-key.  

The *secret-phrase* is something only the authenticated user is privy to.  The *overhide* broker hashes it to create an *identity*.  In the future to prove one's *identity* the user must know the *secret-phrase* such that it hashes to the *identity*, proving the user knows the *secret-phrase* corresponding to said *identity*.

During authentication the broker system also verifies that there is a single mapping from the provided *user-address* and *secret-phrase* to a *broker-salt<sub>hash</sub>(secret-phrase)*.  If the mapping exists and the computed hash doesn't equal the stored hash, the provided *secret-phrase* is different from one provided earlier.  As such the *user-address* already has an *identity* in the system with a different *secret-phrase*, and the *secret-phrase* provided at no cannot be accepted.

If the user remembers the previous *secret-phrase*, the user's original *identity* can be calculated and all *datastore-keys* owned by that *identity* can be changed to be owned by an *identity* with a new *secret-phrase*:  somewhat akin to a password change.  The *user-address* could be changed as part of this operation as well.

If the user doesn't remember the previous *secret-phrase*, the original *identity* cannot be calculated, all *datastore-keys* owned by that *identity* are unrecoverable.

#### Subscriptions

*overhide* isn't concerned with the exact mechanism for subscriptions and recurring subscriptions outside of what's made available with some *remuneration API* implementation.  There is no *overhide* specific token.  Implementations of the *renumeration API* abstract different blockchains for subscription purposes.  

At a minimum a cryptocurrency could be paid from a *user-address* to *broker-address* and a simple transaction timestamp check is all that can be done to set subscription expiration dates.  The *remuneration API* abstracting said blockchain would make this information available to the *overhide* broker.  The *user-address*--being a blockchain address--is used by the *overhide* broker to check a user's subscription and authorities: access levels to the broker system.  If the transaction shows sufficient fees paid sufficiently recently (timestamp check), the user is subscribed.

More capable blockchain implementations could have more advanced smart contracts that make the funds available at a specific date (subscription renewal).  Again, these mechanisms and complexities are hidden behind the specific *remuneration API*.

Regardless of the technology behind a *remuneration API*, the API is there to tell the *overhide* broker if an *identity* is "active" and at what levels.

A subscribed user is a user with an "active" *identity*.  The "active" state of an *identity* is checked explicitly on authentication.  An *overhide* broker tracks two expiration dates for each *identity*:

   1. the *active-until* date: date the *identity* subscription expires.
   1. the *available-until* date: date after which the *overhide* broker may delete all data owned by *identity*.

*active-until* may be same or earlier than *available-until* date.

*active-until* and *available-until* will be updated on authentication as per the most recent payment from *user-address* to *broker-address*.

#### *secret-phrase* Security

Note that the *overhide* broker is privy to the user's *secret-phrase* during runtime.  As such it's possible the *overhide* broker can be used to expose users' *secret-phrases* such that users' data can be deleted or corrupted: the data cannot be deciphered as the *datastore-value* is encrypted by keys known only to the users themselves.  Therefore the *overhide* broker must be trusted to not give up users' *secret-phrases* as they're passed in.  In reality if an *overhide* broker implementation cannot be trusted in this situation, it likely cannot be trusted with it's underlying data storage either.  Such breaches of trust would damage an *overhide* broker's reputation because of the data-loss.

Similarly, an *overhide* broker has no reason to expose *identities*.  These should always be internal to the broker system.

## Two Address Authentication:  Authorize use of Service

The above "Subscriptions" section describes how an *overhide* broker can verify validity of a user's subscription to use the broker.

The same mechanism of looking up transactions from *user-address* to *broker-address* can be used to validate transactions to other public addresses, for example from the *user-address* to a *service-address*.

This is just a utility mechanism and doesn't tie into the *identity* and its *active-until* or *available-until* metadata.

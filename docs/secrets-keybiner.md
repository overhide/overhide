# Secrets and [the Keybiner](https://github.com/overhide/keybiner.js)

## Secrets

To deliver on the value-propositions set out by *overhide* the user is responsible for a handful of addresses, passwords, and tokens.

![Secrets user is responsible for.](images/secrets.svg)

The above figure shows the various values a user needs to provide to an *overhide* [client](https://github.com/overhide/overhide#overhidejs) in order to work with their data.

To indicate which [remuneration ledger](https://github.com/overhide/overhide#remuneration-api) is being used the user provides a *remuneration-key*.  To [authenticate and authorize](https://github.com/overhide/ledgers.js/blob/master/why/why.md) on this *ledger* the user provides their [public-address](glossary.md#user-address).  To prove ownership of the *public-address* the user will need to sign with their [secret-key](glossary.md#private-key--secret-key).

These three values will be second-nature to some users as they will be leveraging a [wallet](glossary.md#wallet).  However, to many other users these values and their purpose will be foreign and confusing.  

Users accessing some *service* with *overhide* data cannot be expected to understand terms and concepts from the world of crypto-currencies.  Users might likely be using either free or US dollar [ledger](https://github.com/overhide/overhide#remuneration-api) entries from [ohledger.com](https://ledger.overhide.io); entries they might not entirely understand.  Although the above terminology is part and parcel of [ohledger.com](https://ledger.overhide.io), they're not something the end-user may need to understand.

Beyond authorization, an *overhide* client encrypts and decrypts user's data client-side.  Decrypted data doesn't leave the *overhide* client.  For this client-side cryptography to work the user needs a human-friendly [secrets-seed](glossary.md#secrets): one per service (application).  A *secrets-seed* is a service-specific passphrase used to generate a further [batch of secrets](glossary.md#secrets) used by the service (application) for the various cryptographic functions in *overhide*.

> (canonical)
>
> A *secret-seed* provides a root secret of a hierarchy of deterministic [secrets](glossary.md#secrets).
>
> A SHA256 hash of the *secret-seed* has its 256 bits (network-order, big-endian) partitioned into groups of 11 bits.  Each 11 bits represents a number up to 2048.  Each 11 bits is a BIP39 like mnemonic index.  These values are fed into the BIP32 [secrets](glossary.md#secrets) generating algorithm.

So far we established that the three [ledger](https://github.com/overhide/overhide#remuneration-api) values discussed first can be re-used amongst different services (applications) by a user (although the *secret-key* is never exposed to any service (application) regardless); but the *secret-seed* should not be re-used and as such the user will come up with a new one for each new service (application).

Lastly we need to store the data encrypted by our *secret-seed* in a broker we authorize against with our three [ledger](https://github.com/overhide/overhide#remuneration-api) values.  First the broker needs to be dereferenced with its *active-broker-host*: a host name or IP address.  Next a [secret-phrase](glossary.md#secret-phrase)--the user's broker specific passphrase--is provided to indicate the user's identity on the broker.  The *secret-phrase* is signed with the user's *secret-key* to assure the broker the *public-address*, and hence the [identity](identity.md), is being used by its rightful owner.

At this point the user is working with their data.  

There are a lot of values at play and not all of them are obviously evident to the uninitiated.  Juggling all these values might be palatable when a user is signing onto a service, but certainly not on each and every login into the service.  To help users with all these values *overhide* provides a widget called the [keybiner](https://github.com/overhide/keybiner.js).

## [Keybiner](https://github.com/overhide/keybiner.js)

Consider that all the *values* introduced in the previous section can be handled together as a group: a [keybiner](https://github.com/overhide/keybiner.js) of all *values* for a single service (application).

The concept is simple, make a multitude of related configuration points--that might as well be Greek to most users--into a single token that's just as obscure, but at least is easy to select, copy, manage.

A [keybiner](https://github.com/overhide/keybiner.js) is a single BASE64 string that can be encrypted, stored, and moved around easily:

* stored in browser password manager / auto-fill
* stored in online password manager like [LastPass](https://www.lastpass.com)
* stored in mobile password manager
* encrypted and copied to clipboard
* encrypted and emailed to oneself for import into other browsers

A [keybiner](https://github.com/overhide/keybiner.js) string is as single value, a single form input--although obfuscated--that's immediately interchangeable with many values, many form inputs.

![Keybiner](images/keybiner.svg)

In the above figure we see the same *values* from before, except they are grouped together under a [keybiner](https://github.com/overhide/keybiner.js).  The user is responsible for providing just this one token.  Should a user desire convenience, remembering to use a specific [keybiner](https://github.com/overhide/keybiner.js) on a Web site is likely a non-issue, as it's an easily detectable (by the browser) form auto-fill; a password-manager trigger.

Relating back to *overhide* specific application--a [keybiner](https://github.com/overhide/keybiner.js) is a collection of related credentials and metadata needed to access a single service or application: crypto key-pairs or *wallet* references, a *secret-phrase*, a *secrets-seed*, metadata for a particular *overhide* broker system and [remuneration provider](remuneration-api.md).  Again, a [keybiner](https://github.com/overhide/keybiner.js) is a set of keys for a particular purpose grouped together.

> [*Aside*]
>
> Another notable utility in the above figure is the use of the [broker-lookup](lookup.md) to resolve the user's [public-address](glossary.md#user-address) into the *active-broker-host* value.  It's an indirection allowing for some data-mobility and less responsibility on the [keybiner](https://github.com/overhide/keybiner.js).

An application will repetitively access a particular *overhide* broker instance with a particular ledger public key (address) for identification.  The corresponding ledger private key--unless provided by a *wallet*--is also needed on-hand--only in-browser--for signatures.  Furthermore, a subset of user's data used in the application is encrypted with *secrets* which are reconstructed from some human friendly *secrets-seed*; that's also required.  For convenience, better user experience, better application flow, it's desirable to store all these identifiers and tokens on the client machine, e.g. in the client browser.  All these identifiers and tokens for a single purpose comprise a [keybiner](https://github.com/overhide/keybiner.js).  Multiple such [keybiners](https://github.com/overhide/keybiner.js) may stored in the client's browser, identifiable by domain.

### In-Browser Helper

The [keybiner](https://github.com/overhide/keybiner.js) can be made available in any browser login page as a widget.  

The [keybiner](https://github.com/overhide/keybiner.js) is a browser-only solution provided by [keybiner.js](https://github.com/overhide/keybiner.js).

[Native](https://github.com/overhide/overhide#overhidec) applications need necessarily leverage Web rendering layers--some sort of a Web view--in order to leverage the [keybiner](https://github.com/overhide/keybiner.js), [ledgers.js](https://www.npmjs.com/package/ledgers.js), and related wallets.  

However; the [keybiner](https://github.com/overhide/keybiner.js) is a single easy to copy token, making a custom native service provisioning simpler.

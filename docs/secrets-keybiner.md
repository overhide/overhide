# Secrets and the Keybiner

## Secrets

To deliver on the value-propositions set out by *overhide* the user is responsible for a handful of addresses, passwords, and tokens.

![Secrets user is responsible for.](images/secrets.svg)

The above figure shows the various values a user needs to provide to an *overhide* [client](../readme.html#overhidejs) in order to work with their data.

To indicate which [remuneration ledger](../readme.html#remuneration-api) is being used the user provides a *remuneration-key*.  To [authenticate and authorize](https://test.ohledger.com/demo/why/why.html) on this *ledger* the user provides their [public-address](glossary.html#user-address).  To prove ownership of the *public-address* the user will need to sign with their [secret-key](glossary.html#private-key--secret-key).

These three values will be second-nature to some users as they will be leveraging a [wallet](glossary.html#wallet).  However, to many other users these values and their purpose will be foreign and confusing.  

Users accessing some *service* with *overhide* data cannot be expected to understand terms and concepts from the world of crypto-currencies.  Users might likely be using either free or US dollar [ledger](../readme.html#remuneration-api) entries from [ohledger.com](https://ohledger.com); entries they might not entirely understand.  Although the above terminology is part and parcel of [ohledger.com](https://ohledger.com), they're not something the end-user may need to understand.

Beyond authorization, a user encrypts and decrypts their *overhide* data client-side.  Decrypted data doesn't leave the *overhide* client.  For this client-side cryptography to work the user needs a human-friendly [secrets-seed](glossary.html#secrets): one per service (application).  A *secrets-seed* is a service-specific passphrase used to generate a further [batch of secrets](glossary.html#secrets) used by the service (application) for the various cryptographic functions in *overhide*.

> (canonical)
>
> A *secret-seed* provides a root secret of a hierarchy of deterministic [secrets](glossary.html#secrets).
>
> A SHA256 hash of the *secret-seed* has its 256 bits (network-order, big-endian) partitioned into groups of 11 bits.  Each 11 bits represents a number up to 2048.  Each 11 bits is a BIP39 like mnemonic index.  These values are fed into the BIP32 [secrets](glossary.html#secrets) generating algorithm.

So although the three [ledger](../readme.html#remuneration-api) values discussed prior can be re-used amongst different services (applications) by a user--and even then the *secret-key* is never exposed to any service (application) regardless--the *secret-seed* should not be re-used and as such the user will come up with a new one for each new service (application).

Lastly we need to store the data encrypted by our *secret-phrase* in a broker we authorize against with our three [ledger](../readme.html#remuneration-api) values.  First the broker needs to be dereferenced with its *active-broker-host*: a host name or IP address.  Next a [secret-phrase](glossary.html#secret-phrase)--the user's broker specific passphrase--is provided to indicate the user's identity on the broker.  The *secret-phrase* is signed with the user's *secret-key* to assure the broker the *public-address*, and hence the [identity](identity.md), is being used by its rightful owner.

At this point the user is working with their data.  

There are a lot of value at play and not all of them are obviously evident to the uninitiated.  Juggling all these values might be palatable when a user is signing onto a service, but certainly not on each and every login into the service.  To help users with all these values *overhide* provides a widget called a *keybiner*.

## Keybiner

Consider that all the *values* introduced in the previous section are grouped together *keyrings*: one *keyring* per all *values* for a single service (application).

Consider further that each *keyring* is accessed with a single human-friendly *value*:  the *secret-seed*:  the same *secret-seed* used to generate [secrets](glossary.html#secrets).

![Keybiner](images/keybiner.svg)

In the above figure we see the same *values* except they are sourced from the *keybiner* instead of our fallible user.  The user is just responsible for two things: remember to use the *keybiner* and remember the *secret-seed*.  Even then, remembering to use the *keybiner* is a non-issue as the service (application) can provide a link to open the widget.

Another notable utility in the above figure is the use of the [broker-lookup](lookup.md) to resolve the user's [public-address](glossary.html#user-address) into the *active-broker-host* value.  It's an indirection allowing for some data-mobility and less responsibility on the *keybiner*.

A *keybiner* is a collection of *keyrings*.  A *keyring* is a collection of related credentials and metadata needed to access a single service or application: crypto key-pairs or *wallet* references, a *secret-phrase*, a *secrets-seed*, metadata for a particular *overhide* broker system and [remuneration provider](remuneration-api.md).  I.e. a *keyring* is a set of keys for a particular purpose grouped together.

An application will repetitively access a particular *overhide* broker instance with a particular ledger public key (address) for identification.  The corresponding ledger private key--unless provided by a *wallet*--is also needed on-hand--only in-browser--for signatures.  Furthermore, a subset of user's data used in the application is encrypted with *secrets* which are reconstructed from some human friendly *secrets-seed*; that's also required.  For convenience, better user experience, better application flow, it's desirable to store all these identifiers and tokens on the client machine, e.g. in the client browser.  All these identifiers and tokens for a single purpose comprise a *keyring*.  Multiple such *keyrings* are stored in the client's *keybiner*.

It's unlikely that private signing keys of a public ledger will be made available to the *keybiner*; they will be kept in the user's *wallets*.  As such the *keyrings* may not store the keys themselves, but references to the keys in their particular *wallet*.

### In-Browser Helper

The *keybiner* can be made available in any browser login page as a modal.  It opens in an `iframe` tied to the *overhide.io* domain, which--if the user uses a properly secured up-to-date browser--ensures the secrets managed by the *keybiner* are not leaked outside.

The *keybiner* is a browser-only solution provided by [oh$.js](https://github.com/overhide/overhide-remuneration.js) as abstracted by the [*overhide.js*](https://github.com/JakubNer/overhide.js) library.  As such the *keybiner* sits alongside in-browser *wallet* implementations and simplifies logins.

[Native](../readme.html#overhidec) applications need necessarily leverage Web rendering layers--some sort of a Web view--in order to leverage the *keybiner*, [oh$.js](https://github.com/overhide/overhide-remuneration.js), and related wallets.

### High Level Data and Workflow Overview

A model of *keybiner*'s data is reproduced below:

```
 'keybiner': {'keyring1':{
                'brokerHostName':'..',
                'remunerationProviderReference':'..',
                'userAddress':'..',
                'userPrivateKeyOrReference':'..',
                'secretPhrase':'..'
              },
              'keyring2':{..},
              ...
              'keyringN':{..}}
```

Each *keyring* has an alias; "keyring1", "keyring2", "keyringN" above.  These are user friendly names, they can be thought of as the user's tags for each *keyring*.  These can tag a particular *keyring* to a specific service or application.  The *keybiner* symmetrically encrypts the value under each *keyring*: each value is AES 256 encrypted with the same *secrets-seed* used to generate *secrets* to encrypt and decrypt service data targeted by the *keyring*.  

When users log into a service for the first time they provide their *public-address* and *secret-key* from some [ledger](remuneration-api.md); whether from a *wallet* or otherwise.  They optionally specify a broker hostname (or IP) unless leveraging data [delegation](broker.html#tag-delegate) or [benefits](broker.html#tag-benefits).  Finally they type in a *secret-phrase* for the broker, *secrets-seed* for the data, and a unique tag.  All this metadata is sent to the *keybiner*: *overhide.io* bound iframe off of the login page.  In the *keybiner* the data is symmetrically encrypted with the *secrets-phrase* and stored in the browser's *localstorage*.  The user logs into the service.

Upon a return visit to the service they user pops-open the *keybiner* from the login page and types in their *secrets-seed*.  All *keyrings* in this browser's *keybiner* *localstorage* attempt to decrypt.  All successfully decrypted *keyrings* are shown.  The user can select the appropriate tag to send all the metadata to the login page.  Hence the the symmetric *secrets-seed* and choice of *tag* are the access credentials--all local to the browser.  From this point forward the application has all the information it needs to synchronize with the specified *overhide* instance ('brokerUrl').  

The *overhide.js* JavaScript library makes available the *keybiner* module for use by Web applications running in browsers.  The *keybiner* uses *localstorage* to store *keyrings*.  As such browser security restrictions imply *keyrings* are tied to a particular domain.

Each *keyring* can be exported or re-setup from scratch--it's a convenience store to aid end-user experience.  For example:

* an end-user can export their *keybiner* as an encrypted file, store it in the cloud, import it into another browser
* an end-user can encrypt their *keybiner* and email themselves; opening the email on another device and pasting the contents into another browser's *keybiner*
* an end-user can pop-up a QR code of a *keyring* on their screen and use their mobile camera to setup the *keyring* in their mobile browser's *keybiner*

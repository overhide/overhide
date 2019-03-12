# Secrets and the Keybiner

## Keybiner

A *keybiner* is a collection of *keyrings*.  A *keyring* is a collection of related credentials and metadata needed to access a single service or application: crypto key-pairs or *wallet* references, a *secret-phrase*, a *secrets-seed*, metadata for a particular *overhide* broker system and [remuneration provider](remuneration-api.md).  I.e. a *keyring* is a set of keys for a particular purpose grouped together.

An application will repetitively access a particular *overhide* broker instance with a particular ledger public key (address) for identification.  The corresponding ledger private key--unless provided by a *wallet*--is also needed on-hand--only in-browser--for signatures.  Furthermore, a subset of user's data used in the application is encrypted with *secrets* which are reconstructed from some human friendly *secrets-seed*; that's also required.  For convenience, better user experience, better application flow, it's desirable to store all these identifiers and tokens on the client machine, e.g. in the client browser.  All these identifiers and tokens for a single purpose comprise a *keyring*.  Multiple such *keyrings* are stored in the client's *keybiner*.

It's unlikely that private signing keys of a public ledger will be made available to the *keybiner*; they will be kept in the user's *wallets*.  As such the *keyrings* may not store the keys themselves, but references to the keys in their particular *wallet*.

### In-Browser Helper

The *keybiner* can be made available in any browser login page as a modal.  It opens in an `iframe` tied to the *overhide.io* domain, which--if the user uses a properly secured up-to-date browser--ensures the secrets managed by the *keybiner* are not leaked outside.

The *keybiner* is a browser-only solution provided by [oh$.js](https://github.com/overhide/overhide-remuneration.js) as abstracted by the [*overhide.js*](https://github.com/JakubNer/overhide.js) library.  As such the *keybiner* sits alongside in-browser *wallet* implementations and simplifies logins.

Native applications need necessarily leverage Web rendering layers--some sort of a Web view--in order to leverage the *keybiner*, [oh$.js](https://github.com/overhide/overhide-remuneration.js), and related wallets.

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

When users log into a service for the first time they provide their *user-address* and *secret-key* from some [ledger](remuneration-api.md); whether from a *wallet* or otherwise.  They optionally specify a broker hostname (or IP) unless leveraging data [delegation](broker.html#tag-delegate) or [benefits](broker.html#tag-benefits).  Finally they type in a *secret-phrase* for the broker, *secrets-seed* for the data, and a unique tag.  All this metadata is sent to the *keybiner*: *overhide.io* bound iframe off of the login page.  In the *keybiner* the data is symmetrically encrypted with the *secrets-phrase* and stored in the browser's *localstorage*.  The user logs into the service.

Upon a return visit to the service they user pops-open the *keybiner* from the login page and types in their *secrets-seed*.  All *keyrings* in this browser's *keybiner* *localstorage* attempt to decrypt.  All successfully decrypted *keyrings* are shown.  The user can select the appropriate tag to send all the metadata to the login page.  Hence the the symmetric *secrets-seed* and choice of *tag* are the access credentials--all local to the browser.  From this point forward the application has all the information it needs to synchronize with the specified *overhide* instance ('brokerUrl').  

The *overhide.js* JavaScript library makes available the *keybiner* module for use by Web applications running in browsers.  The *keybiner* uses *localstorage* to store *keyrings*.  As such browser security restrictions imply *keyrings* are tied to a particular domain.

Each *keyring* can be exported or re-setup from scratch--it's a convenience store to aid end-user experience.  For example:

* an end-user can export their *keybiner* as an encrypted file, store it in the cloud, import it into another browser
* an end-user can encrypt their *keybiner* and email themselves; opening the email on another device and pasting the contents into another browser's *keybiner*
* an end-user can pop-up a QR code of a *keyring* on their screen and use their mobile camera to setup the *keyring* in their mobile browser's *keybiner*

# *overhide.js*

*overhide.js* is the JavaScript client interface to the *overhide* system.  

Its intent is to be an all-encompassing library enabling access to all features of the *overhide* system.

![overhide.js detail](images/overhidejs.png)

*Application code* and *tooling* are written against the *overhide* interface.

The *overhide* interface is made concrete by [*overhide.js*](https://github.com/JakubNer/overhide.js).  [*overhide.js*](https://github.com/JakubNer/overhide.js) is brought in by *application code* as a dependency.

[*overhide.js*](https://github.com/JakubNer/overhide.js) depends on [oh$.js](https://github.com/overhide/overhide-remuneration.js) to abstract interaction with public ledgers--to the extent that is needed by *overhide*.  For example the *web3*, [*overhide-ledger*](https://ohledger.com), and *?*, interfaces modeled.  The application's customers interact with their [*wallets*](docs/glossary.md#wallet) of choice, whose interfaces to *overhide* are normalized with [oh$.js](https://github.com/overhide/overhide-remuneration.js).

[*overhide.js*](https://github.com/JakubNer/overhide.js) is injected with [oh$.js](https://github.com/overhide/overhide-remuneration.js) which targets ledger credentials an app user wants to use when authorizing with *overhide*.  The [keybiner](glossary.html#keybiner--keyrings) is an optional cache of ledger credentials; caching credentials between sessions with user-chosen passwords.  The [keybiner](glossary.html#keybiner--keyrings)--if used--provides ledger credentials from it's cache to [oh$.js](https://github.com/overhide/overhide-remuneration.js).

Hence a user of some *application code* uses their wallet of choice, with some or all credential cached through [keybiner](glossary.html#keybiner--keyrings).  [Keybiner](glossary.html#keybiner--keyrings)--having a user-chosen password--is human friendly on a daily basis.  Regardless, the user's ledger credentials make their way to [*overhide.js*](https://github.com/JakubNer/overhide.js) through [oh$.js](https://github.com/overhide/overhide-remuneration.js).

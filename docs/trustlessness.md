# Broker Trustlessness

> "I trust you as far as I can throw you" -- unknown

Core to *overhide* are the values of the system not knowing who the clients are or what their data is.  The clients are just [identities](identity.md) and the data is [encrypted](glossary.html#datastore-value).

Trust doesn't factor into the brokers' ability to read client data or leverage client metadata--that's overtly hidden with cryptography.

However; the brokers are trusted to write out and host their clients' encrypted data on an as-far-as-they-can-be-thrown basis--'they' being the brokers.

As long as they're allowed, each broker writes to the distributed persistence network on behalf of its clients.  For example the [reference implementation](https://github.com/JakubNer/overhide-broker) broker has the IPNS PKI pairs of its clients and is trusted to write out the data as dictated.

*This is tantamount to a thin blockchain client's trust that a full-node writes out a transaction as dictated.*

A deceitful blockchain node could tell the client it included a transaction when it didn't.  The client can keep the node honest by double checking with the blockchain.

Similarly an *overhide* broker could skip-out on eventual consistency of clients' data with its distributed persistence network, not host (on behalf of the network)/pin it, or later delete clients' data.  The clients can keep the broker honest by double checking with the network or other brokers.

A deceitful blockchain node could corrupt a transaction--although that would be caught immediately by verifying peers.

Similarly an *overhide* broker could corrupt a clients data; propagating a corrupted version to the network.  Although other *overhide* brokers do not validate data--an important distinction from blockchain peers--the client can [validate via content hashes](broker.html#operation---segment-key--persistence-status-get).

#### Keep In Mind

Brokers have an economic incentive to keep subscribers happy: subscribers aren't locked in and can drop a bad broker at any time: see ["Sample Use Case: Disassociation from Steward"](broker.html#tag-data-stewardship).

A compromised broker can't target a victim.  Users are pseudonymous hence a broker compromise can only be indiscriminate and have the same chance to negatively affect all users.

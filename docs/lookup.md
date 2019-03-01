# Discovery and DNS: Find Data with Broker-Lookup

## Motivation

To use *overhide* data with some service a user needs to tell the service which *overhide* broker the service should work with.

The user will select an *overhide* broker on criteria which--among others--include the ledger the user wants to work through for [remuneration and authorization](../readme.html#remuneration-api).

This choice boils down to which ledger's public-address the user wants to access their *overhide* data with.  This [user-address](glossary.html#user-address) must be from a ledger supported by the chosen broker as per the broker's available [remuneration keys](broker.html#operation--capabilities-remuneration-providers-get).  

These decisions are made once, during onboarding onto a service.

After that--on all subsequent interactions--in order for a user to access their data they must qualify this access with their:

* [user-address](glossary.html#user-address)
* the ledger this [user-address](glossary.html#user-address) lives on: the [remuneration key](broker.html#/definitions/RemunerationKey)
* the host name or IP of the broker hosting this data.

The purpose of [broker-lookup](broker.html#tag-broker-lookup) is to enable users to login into their *overhide* supporting services with just the [user-address](glossary.html#user-address): without requiring the service to ask for, or the user to have to enter, ledger ([remuneration key](broker.html#/definitions/RemunerationKey)) or broker (host name, IP) information.

It's envisioned that in case of crypto-currencies the [user-address](glossary.html#user-address) is something the user commonly interacts with through their crypto-wallet: the wallet signs on behalf of the [user-address](glossary.html#user-address).  

In case of fiat money--where [overhide-ledger](https://ohledger.com) is used for US dollars--the [user-address](glossary.html#user-address) might not be in a crypto-wallet and as such its *secret-key* is directly handled by the user.  When a [user-address](glossary.html#user-address) is handled directly with its *secret-key*, this can be considered similar to a *username* and *password* in traditional login setups.

Hence a [user-address](glossary.html#user-address) can be though of as a fairly household concept.  Since [broker-lookup](broker.html#tag-broker-lookup) allows users to discover the other information from just the [user-address](glossary.html#user-address), it makes for a streamlined user experience.

## High Level Design Details

-> peer mesh
-> ok with eth and bitcoin

## Reference Implementation

-> DHT / Kademlia
-> kadence
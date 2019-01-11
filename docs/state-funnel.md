# The State Funnel -- On Thick Client Thin Client Ambivalence and Relations in a Key-Value Store

The *overhide* system is here as an alternative backend for apps and services.  It provides authentication, authorization, and a data store.  But does it apply in many projects?  Can you fit it in your architecture?

This write-up is meant to provoke a way of thinking when writing a meaningful collaborative application without a relational database, with just a key-value store.

It's all in the title:

* **state funnel**: state of data is funneled via [backchannels](http://overhide.io/overhide/docs/glossary.html#backchannel-queue) to update [segment-key](http://overhide.io/overhide/docs/glossary.html#segment-key) values in a gated fashion
* **thick client think client ambivalence**: *overhide* provided data crunching is done--or at least mediated--client-side (instead of a back-end); regardless of how you position your client code in terms of your architecture (business-logic wise): n-tier, serverless, very thick (video game)
* **relations**: this isn't a relational store, but you can certainly store relations; with *overhide*'s segmentation of data by owner--e.g. an individual, a team, a group--consider how references to tightly correlated key-values can be leveraged over a long constantly filtered tables


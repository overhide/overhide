# The State Funnel -- On Thick Client Thin Client Ambivalence and Relations in a Key-Value Store

The *overhide* system is here as an alternative backend for apps and services.  It provides authentication, authorization, and a data store.  But does it apply in many projects?  Can you fit it in your architecture?

This write-up is meant to provoke a way of thinking when writing a meaningful collaborative application without a relational database, with just a key-value store.

It's all in the title:

* **state funnel**: state of data is funneled via [backchannels](http://overhide.io/overhide/docs/glossary.html#backchannel-queue) to update [segment-key](http://overhide.io/overhide/docs/glossary.html#segment-key) values in a gated fashion
* **thick client think client ambivalence**: *overhide* provided data crunching is done--or at least mediated--client-side (instead of a back-end)
* **relations**: this isn't a relational store, but you can certainly store relations; with *overhide*'s segmentation of data by owner--e.g. an individual, a team, a group--consider how references to tightly correlated key-values can be effective over a long constantly filtered tables

The rest of this write-up will be an example detailing out flow of data between team-members--each using a mobile app to collaborate on a common data-set.  Each user's app is a thick client that *funnels* state transitions to keep data *relations* consistent.  The only backend is the *overhide* system.


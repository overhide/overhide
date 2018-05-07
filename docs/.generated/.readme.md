# Rendering Markdown API Files

Files in this folder are generated from files in [\docs](\docs).

Commands below are expected to be run from [\docs](\docs).

Do not edit files herein directly.

##### broker.html, remuneration.html

Generated from the *broker.yaml* and *remuneration.yaml* Swagger yaml files.

These are rendered into HTML with bootprint/bootprint-openapi (https://github.com/bootprint/bootprint-openapi):

One-time install dependencies:

```
npm install -g bootprint
npm install -g bootprint-openapi
npm install -g html-inline
```

Generate *broker.html*:

```
bootprint openapi broker.yaml .generated/broker
html-inline .generated/broker/index.html > broker.html
```

Generate *remuneration.html*:

```
bootprint openapi remuneration.yaml .generated/remuneration
html-inline .generated/remuneration/index.html > remuneration.html
```

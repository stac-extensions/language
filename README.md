# Language (I18N) Extension Specification

- **Title:** Language (I18N)
- **Identifier:** <https://stac-extensions.github.io/language/v1.0.0/schema.json>
- **Field Name Prefix:** -
- **Scope:** Item, Catalog, Collection
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-spec/tree/master/extensions/README.md#extension-maturity):** Proposal
- **Owner**: @m-mohr

This document is the Language (I18N) Extension to the
[SpatioTemporal Asset Catalog](https://github.com/radiantearth/stac-spec) (STAC) specification,
which explains fields and recommendations around making multi-lingual STAC catalogs available.

The focus of this extension is to make multi-lingual static STAC catalogs available.
There's also a dedicated [language extension for STAC APIs](https://github.com/stac-api-extensions/language).
So there's a *STAC* Language extension **and** a *STAC API* Langauge extension.

- Examples:
  - Catalogs: [English](examples/catalog.json) / [German](examples/de/catalog.json) / [Arabic](examples/ar/catalog.json)
  - Items: [English](examples/item.json) / [German](examples/de/item.json) / [Arabic](examples/ar/item.json)
- [JSON Schema](json-schema/schema.json)
- [Changelog](CHANGELOG.md)

## Fields for Catalogs, Collections and Item Properties

The fields in the table below can be used in these parts of STAC documents:
- [x] Catalogs
- [x] Collections
- [x] Item Properties (incl. Summaries in Collections)
- [ ] Assets (for both Collections and Items, incl. Item Asset Definitions in Collections)
- [ ] Links

| Field Name | Type                                   | Description |
| ---------- | -------------------------------------- | ----------- |
| language   | [Language Object](#language-object)    | **REQUIRED**. The language of the document. |
| languages  | \[[Language Object](#language-object)] | Other languages the document is available in. This list MUST NOT contain the language of the document. |

*Note:* OGC API - Records defines an additional field `resourceLanguages` to specify the list of languages
the resource (assets) being described by the Record (Item, Catalog, or Collection) is available in.
For details see [list of languages for assets](#list-of-languages-for-assets).

### Language Object

Each Language Object describes an individual language.
Implementors may add additional properties for their usecases (e.g. for formatting numbers or dates and times).

| Field Name | Type   | Description |
| ---------- | ------ | ----------- |
| code       | string | **REQUIRED**. This MUST be the valid `Language-Tag` for the language as specified in [RFC 5646](https://www.rfc-editor.org/rfc/rfc5646). |
| name       | string | The name of the language in the language itself (e.g. "Deutsch" for German). This MUST NOT be translated. |
| alternate  | string | The name of the language in another well-understood language, usually English (e.g. "German" for German). |
| dir        | string | The direction for text in this language. Either `ltr` (left-to-right) or `rtl` (right-to-left). Defaults to `ltr`. |

#### alternate

It is a good practice to provide language names in two languages so that they can be understood both by
users that *speak* and *don't speak* the language.
The `name` is always in the language of the language itself and caters for users that *speak* this language.
`alternate` caters for users that *don't speak* the language.
It is often English, but could be something else, e.g., it could be in the language of the document itself.

## Fields for Links and Assets

The fields in the table below can be used in these parts of STAC documents:
- [ ] Catalogs
- [ ] Collections
- [ ] Item Properties (incl. Summaries in Collections)
- [x] Assets (for both Collections and Items, incl. Item Asset Definitions in Collections)
- [x] Links

| Field Name | Type   | Description |
| ---------- | ------ | ----------- |
| hreflang   | string | The language to be expected for the `href` in the link or asset. The language MUST BE a valid `Language-Tag` as specified in [RFC 5646](https://www.rfc-editor.org/rfc/rfc5646). |

## Best practices

- The `alternate` relation type should be used to provide links to the same resource, but in other languages.
  Be aware that alternative representations can also point to alternative media types, e.g. an HTML representation of the JSON files.
  So to get all links to (Geo)JSON files for the various languages, you also need to check the media `type`.
- Other links to STAC documents (e.g. for relation types `item`, `child`, `parent`, `root`)
  should only be provided in the language present in the current document.
- STAC Assets should always be provided in all languages.
- The STAC files for the individual languages should be provided in separate subfolders,
  but the default language can be provided in the parent of the sub-folders
  (for an example please see the [examples folder](examples/)).

## Relation to other specifications

The specification aims for alignment with
- [OGC API - Common](https://ogcapi.ogc.org/common/)
- [OGC API - Features](https://ogcapi.ogc.org/features/)
- [OGC API - Records](https://ogcapi.ogc.org/records/)
- [RFC 5646 (Tags for Identifying Languages)](https://www.rfc-editor.org/rfc/rfc5646)
- [RFC 8288 (Web Linking)](https://www.rfc-editor.org/rfc/rfc8288.html)

More specifically, the `language` and `languages` properties are aligned with
[OGC API - Records](http://docs.ogc.org/DRAFTS/20-004.html#core-queryables-resource-table).
The `hreflang` property is defined in RFC 8288 and various OGC APIs, e.g. 
[Features](https://docs.opengeospatial.org/is/17-069r4/17-069r4.html#string_i18n),
[Records](http://docs.ogc.org/DRAFTS/20-004.html#sc_templated_links_with_variables) and
[Common](http://docs.ogc.org/DRAFTS/19-072.html#string-internationalization-section).

### List of languages for assets

OGC API - Records defines an additional field `resourceLanguages` to specify the list of languages
the resource (in STAC: Assets) being described by the Record (in STAC: Item, Catalog, or Collection) is available in.
The field name might be confusing to STAC users as the term "resources" in STAC would be "assets".
In many cases, assets are not translatable in STAC (e.g. raster imagery), but you can still get a list
of language codes by reading the `hreflang` properties for all assets.

Examples in JavaScript:
```js
let assetLanguages = new Set();
Object.values(stac.assets)
  .filter(asset => typeof asset.hreflang === 'string')
  .forEach(asset => assetLanguages.add(asset.hreflang));
```

```js
let assetLanguages = [];
for(key in stac.assets) {
  let asset = stac.assets[key]
  if (typeof asset.hreflang === 'string' && !assetLanguages.includes(asset.hreflang)) {
    assetLanguages.push(asset.hreflang);
  }
}
```

## Implementations

The following client and/or server software implement this extension:

- [STAC Browser](https://github.com/radiantearth/stac-browser)

## Contributing

All contributions are subject to the
[STAC Specification Code of Conduct](https://github.com/radiantearth/stac-spec/blob/master/CODE_OF_CONDUCT.md).
For contributions, please follow the
[STAC specification contributing guide](https://github.com/radiantearth/stac-spec/blob/master/CONTRIBUTING.md) Instructions
for running tests are copied here for convenience.

### Running tests

The same checks that run as checks on PR's are part of the repository and can be run locally to verify that changes are valid. 
To run tests locally, you'll need `npm`, which is a standard part of any [node.js installation](https://nodejs.org/en/download/).

First you'll need to install everything with npm once. Just navigate to the root of this repository and on 
your command line run:
```bash
npm install
```

Then to check markdown formatting and test the examples against the JSON schema, you can run:
```bash
npm test
```

This will spit out the same texts that you see online, and you can then go and fix your markdown or examples.

If the tests reveal formatting problems with the examples, you can fix them with:
```bash
npm run format-examples
```

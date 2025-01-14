# docusaurus-plugin-typedoc

A [Docusaurus v2](https://v2.docusaurus.io/) plugin to build documentation with [TypeDoc](https://github.com/TypeStrong/typedoc).

[![npm](https://img.shields.io/npm/v/docusaurus-plugin-typedoc.svg)](https://www.npmjs.com/package/docusaurus-plugin-typedoc)
![CI](https://github.com/tgreyuk/typedoc-plugin-markdown/actions/workflows/ci.yml/badge.svg?branch=master)

## What it does?

- Generates static TypeDoc pages in Markdown as part of the build.
- Adds frontmatter to pages.

## Installation

> Install in the same location as the Docusaurus website directory.

This guide assumes that a Docusaurus project has already been setup. See [installation docs](https://v2.docusaurus.io/docs/installation).

```shell
npm install typedoc typedoc-plugin-markdown docusaurus-plugin-typedoc --save-dev
```

## Usage

### Config

Add the plugin to `docusaurus.config.js` and specify the required options (see [options](#options)).

```js
module.exports = {
  plugins: [
    [
      'docusaurus-plugin-typedoc',

      // Plugin / TypeDoc options
      {
        entryPoints: ['../src/index.ts'],
        tsconfig: '../tsconfig.json'
      },
    ],
  ],
};
```

TypeDoc will be bootstraped with the Docusaurus `start` and `build` [cli commands](https://v2.docusaurus.io/docs/cli):

```javascript
"start": "docusaurus start",
"build": "docusaurus build",
```

Once built the docs will be available at `/docs/api` or equivalent out directory.

```
website/ (docusaururs website root)
├── build/ (static site dir)
├── docs/
│   ├── api/ (compiled typedoc markdown)
├── docusaurus.config.js
├── package.json
├── sidebars.js
```

### Sidebar

`sidebars.js` can be configured in following ways:

1 ) Generate the entire sidebar from file structure of your docs folder (default behaviour):

```js
module.exports = {
  someSidebar: [
    {
      type: 'autogenerated',
      dirName: '.', // '.' means the docs folder
    },
  ],
};
```

2 ) Alternatively, if you wish to manually control other parts of your sidebar you can use a slice for the TypeDoc sidebar.
(note:`sidebar.categoryLabel` and `sidebar.position` options are ignored with this implementation)


```js
module.exports = {
  someSidebar: {
    'Category 1': ['doc1', 'doc2', 'doc3'],

    // use seperate generated file
    'API': require('./typedoc-sidebar.js'),

    // or write inline
    'API': [
      {
        type: 'autogenerated',
        dirName: 'api', // 'api' is the 'out' directory
      },
    ],
  },
};
```

> To maintain backward compatibility with previous versions a `./typedoc-sidebar.js` is created by default. Pass `sidebar.sidebarFile:null` to prevent this.

Please see https://docusaurus.io/docs/sidebar for sidebar documentation.

## Options

### TypeDoc options

To configure TypeDoc, pass any relevant [TypeDoc options](https://typedoc.org/guides/options/) to the config.

At a minimum the `entryPoints` and `tsconfig` options will need to be set.

```js
entryPoints: ['../src/index.ts'],
tsconfig: '../tsconfig.json'
```

Additional TypeDoc plugins will need to be explicitly set:

```js
plugin:['typedoc-plugin-xyz']
```

#### Other config options

TypeDoc options can also be declared:

- Using a `typedoc.json` file.
- Under the `typedocOptions` key in `tsconfig.json`.

Note: Options declared in this manner will take priority and overwrite options declared in `docusaurus.config.js`.

### Plugin options

| Name                    | Default | Description                                  |
| :---------------------- | :------ | :------------------------------------------- |
| `out`                   | `"api"` | Output directory relative to docs directory. |
| `sidebar.categoryLabel` | `API`   | The sidebar parent category label.           |
| `sidebar.fullNames`     | `false` | Display full names with module path.         |
| `sidebar.position`      | `null`  | The position of the sidebar in the tree.     |

If the manual sidebar is not required pass `sidebar.sidebarFile: null` to skip sidebar generation.

### An example configuration

```js
module.exports = {

  plugins: [
    [
      'docusaurus-plugin-typedoc',
      {
        // TypeDoc options
        entryPoints: ['../src/index.ts'],
        tsconfig: '../tsconfig.json',
        plugin:['typedoc-plugin-xyz'],

        // Plugin options
        out: 'api-xyz',
        sidebar: {
          categoryLabel: 'API XYZ',
          position: 0,
          fullNames: true
        },
      },
    ],
  ],
};
```


## Additional config

### Multi instance

It is possible to build multi TypeDoc instances by passing in multiple configs with unique ids:

`docusaurus.config.js`
```js
module.exports = {
  plugins: [
    [
      'docusaurus-plugin-typedoc',
      {
        id:'api-1',
        entryPoints: ['../api-1/src/index.ts'],
        tsconfig: '../api-1/tsconfig.json',
      },
    ],
    [
      'docusaurus-plugin-typedoc',
      {
        id:'api-2',
        entryPoints: ['../api-2/src/index.ts'],
        tsconfig: '../api-2/tsconfig.json',
      },
    ],
  ],
};
```

### Watch mode

Watching files is supported by passing in the `watch: true` option see [https://typedoc.org/guides/options/#watch](https://typedoc.org/guides/options/#watch).

Targetting the option in development mode only can be achieved using Node.js Environment Variables:

`package.json`

```json
"start": "TYPEDOC_WATCH=true docusaurus start",
"build": "TYPEDOC_WATCH=false docusaurus build",
```

`docusaurus.config.js`

```js
module.exports = {
  plugins: [
    [
      'docusaurus-plugin-typedoc',
      {
        entryPoints: ['../src/index.ts'],
        tsconfig: '../tsconfig.json',
        watch: process.env.TYPEDOC_WATCH,
      },
    ],
  ],
};
```

## License

[MIT](https://github.com/tgreyuk/typedoc-plugin-markdown/blob/master/packages/docusaurus-plugin-typedoc/LICENSE)

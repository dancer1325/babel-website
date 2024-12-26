---
id: plugins
title: Plugins
---

* ways to enable Babel's code transformations
  * apply plugins | your [configuration file](config-files.md)
  * apply [presets](presets.md)

## Using a Plugin

* set | [plugins](options.md#presets) config option
  * ways to specify
    * name of the plugin
      * plugin exists | [npm](https://www.npmjs.com/search?q=babel-plugin)
      * Babel checks that it's installed | `node_modules`
      * _Example:_

        ```json title="babel.config.json"
        {
          "plugins": ["babel-plugin-myPlugin", "@babel/plugin-transform-runtime"]
        }
        ```
    * relative/absolute path -- to -- your plugin
      * see [name normalization](options.md#name-normalization)
      * _Example:_

      ```json title="babel.config.json"
      {
        "plugins": ["./node_modules/asdf/plugin"]
      }
      ```

## Transform Plugins

* == apply transformations | your code
* 👀enable the corresponding syntax plugin 👀
  * -> you do NOT have to specify BOTH

## Syntax Plugins

* MOST syntax -- is transformable by -- Babel
* | rarer cases, (_Example:_ transform is NOT implemented yet, or there is NOT a default way to do so)
  * if you want ONLY allow Babel to parse specific types of syntax -> use specific plugins (_Example:_ `@babel/plugin-syntax-bigint`)
  * if you want to preserve the source code
    * _Example:_ ONLY want Babel to do code analysis or codemods

* Babel parser's [`plugins` option](parser.md#plugins)
  * _Example:_

  ```json title="JSON, title=.babelrc.json"
  {
    "parserOpts": {
      "plugins": ["jsx", "flow"]
    }
  }
  ```

## Plugin Ordering

* TODO:
> Ordering matters for each visitor in the plugin.

This means if two transforms both visit the "Program" node, the transforms will run in either plugin or preset order.

- Plugins run before Presets.
- Plugin ordering is first to last.
- Preset ordering is reversed (last to first).

For example:

```json title="babel.config.json"
{
  "plugins": ["transform-decorators-legacy", "transform-class-properties"]
}
```

Will run `transform-decorators-legacy` then `transform-class-properties`.

It is important to remember that with presets, the order is _reversed_. The following:

```json title="babel.config.json"
{
  "presets": ["@babel/preset-env", "@babel/preset-react"]
}
```

Will run in the following order: `@babel/preset-react` then `@babel/preset-env`.

## Plugin Options

Both plugins and presets can have options specified by wrapping the name and an options object in an array inside your config.

For specifying no options, these are all equivalent:

```json title="babel.config.json"
{
  "plugins": ["pluginA", ["pluginA"], ["pluginA", {}]]
}
```

To specify an option, pass an object with the keys as the option names.

```json title="babel.config.json"
{
  "plugins": [
    [
      "transform-async-to-module-method",
      {
        "module": "bluebird",
        "method": "coroutine"
      }
    ]
  ]
}
```

Settings options for presets works exactly the same:

```json title="babel.config.json"
{
  "presets": [
    [
      "env",
      {
        "loose": true,
        "modules": false
      }
    ]
  ]
}
```

## Plugin Development

Please refer to the excellent [babel-handbook](https://github.com/thejameskyle/babel-handbook)
to learn how to create your own plugins.

The simple plugin that reverses names (from the homepage):

```js title="JavaScript"
export default function() {
  return {
    visitor: {
      Identifier(path) {
        const name = path.node.name;
        // reverse the name: JavaScript -> tpircSavaJ
        path.node.name = name
          .split("")
          .reverse()
          .join("");
      },
    },
  };
}
```

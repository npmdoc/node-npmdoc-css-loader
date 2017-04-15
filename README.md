# api documentation for  [css-loader (v0.28.0)](https://github.com/webpack/css-loader#readme)  [![npm package](https://img.shields.io/npm/v/npmdoc-css-loader.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-css-loader) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-css-loader.svg)](https://travis-ci.org/npmdoc/node-npmdoc-css-loader)
#### css loader module for webpack

[![NPM](https://nodei.co/npm/css-loader.png?downloads=true&downloadRank=true&stars=true)](https://www.npmjs.com/package/css-loader)

[![apidoc](https://npmdoc.github.io/node-npmdoc-css-loader/build/screenCapture.buildCi.browser.apidoc.html.png)](https://npmdoc.github.io/node-npmdoc-css-loader/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-css-loader/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-css-loader/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Tobias Koppers @sokra"
    },
    "bugs": {
        "url": "https://github.com/webpack/css-loader/issues"
    },
    "dependencies": {
        "babel-code-frame": "^6.11.0",
        "css-selector-tokenizer": "^0.7.0",
        "cssnano": ">=2.6.1 <4",
        "loader-utils": "^1.0.2",
        "lodash.camelcase": "^4.3.0",
        "object-assign": "^4.0.1",
        "postcss": "^5.0.6",
        "postcss-modules-extract-imports": "^1.0.0",
        "postcss-modules-local-by-default": "^1.0.1",
        "postcss-modules-scope": "^1.0.0",
        "postcss-modules-values": "^1.1.0",
        "source-list-map": "^0.1.7"
    },
    "description": "css loader module for webpack",
    "devDependencies": {
        "codecov": "^1.0.1",
        "eslint": "3.14.0",
        "istanbul": "^0.4.5",
        "mocha": "^3.2.0",
        "should": "^11.1.2",
        "standard-version": "^4.0.0"
    },
    "directories": {},
    "dist": {
        "shasum": "417cfa9789f8cde59a30ccbf3e4da7a806889bad",
        "tarball": "https://registry.npmjs.org/css-loader/-/css-loader-0.28.0.tgz"
    },
    "engines": {
        "node": ">=0.12.0 || >=4.3.0 <5.0.0 || >=5.10"
    },
    "files": [
        "index.js",
        "locals.js",
        "lib"
    ],
    "gitHead": "2f562f8c3a8a222dd3a5d66b44e219e5b2e8fd2f",
    "homepage": "https://github.com/webpack/css-loader#readme",
    "license": "MIT",
    "maintainers": [
        {
            "name": "bebraw"
        },
        {
            "name": "d3viant0ne"
        },
        {
            "name": "ericclemmons"
        },
        {
            "name": "jhnns"
        },
        {
            "name": "markdalgleish"
        },
        {
            "name": "sokra"
        },
        {
            "name": "spacek33z"
        },
        {
            "name": "thelarkinn"
        }
    ],
    "name": "css-loader",
    "optionalDependencies": {},
    "repository": {
        "type": "git",
        "url": "git+ssh://git@github.com/webpack/css-loader.git"
    },
    "scripts": {
        "cover": "istanbul cover node_modules/mocha/bin/_mocha",
        "lint": "eslint lib test",
        "release": "yarn run standard-version",
        "test": "mocha",
        "test:cover": "npm run cover -- --report lcovonly",
        "travis:lint": "npm run lint",
        "travis:test": "npm run cover"
    },
    "version": "0.28.0"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module css-loader](#apidoc.module.css-loader)
1.  [function <span class="apidocSignatureSpan"></span>css-loader (content, map)](#apidoc.element.css-loader.css-loader)
1.  [function <span class="apidocSignatureSpan">css-loader.</span>toString ()](#apidoc.element.css-loader.toString)



# <a name="apidoc.module.css-loader"></a>[module css-loader](#apidoc.module.css-loader)

#### <a name="apidoc.element.css-loader.css-loader"></a>[function <span class="apidocSignatureSpan"></span>css-loader (content, map)](#apidoc.element.css-loader.css-loader)
- description and source-code
```javascript
css-loader = function (content, map) {
	if(this.cacheable) this.cacheable();
	var callback = this.async();
	var query = loaderUtils.getOptions(this) || {};
	var root = query.root;
	var moduleMode = query.modules || query.module;
	var camelCaseKeys = query.camelCase || query.camelcase;
	var resolve = createResolver(query.alias);

	if(map !== null && typeof map !== "string") {
		map = JSON.stringify(map);
	}

	processCss(content, map, {
		mode: moduleMode ? "local" : "global",
		from: loaderUtils.getRemainingRequest(this),
		to: loaderUtils.getCurrentRequest(this),
		query: query,
		minimize: this.minimize,
		loaderContext: this
	}, function(err, result) {
		if(err) return callback(err);

		var cssAsString = JSON.stringify(result.source);

		// for importing CSS
		var importUrlPrefix = getImportPrefix(this, query);

		var alreadyImported = {};
		var importJs = result.importItems.filter(function(imp) {
			if(!imp.mediaQuery) {
				if(alreadyImported[imp.url])
					return false;
				alreadyImported[imp.url] = true;
			}
			return true;
		}).map(function(imp) {
			if(!loaderUtils.isUrlRequest(imp.url, root)) {
				return "exports.push([module.id, " +
					JSON.stringify("@import url(" + imp.url + ");") + ", " +
					JSON.stringify(imp.mediaQuery) + "]);";
			} else {
				var importUrl = importUrlPrefix + imp.url;
				return "exports.i(require(" + loaderUtils.stringifyRequest(this, importUrl) + "), " + JSON.stringify(imp.mediaQuery) + ");";
			}
		}, this).join("\n");

		function importItemMatcher(item) {
			var match = result.importItemRegExp.exec(item);
			var idx = +match[1];
			var importItem = result.importItems[idx];
			var importUrl = importUrlPrefix + importItem.url;
			return "\" + require(" + loaderUtils.stringifyRequest(this, importUrl) + ").locals" +
				"[" + JSON.stringify(importItem.export) + "] + \"";
		}

		cssAsString = cssAsString.replace(result.importItemRegExpG, importItemMatcher.bind(this));
		if(query.url !== false) {
			cssAsString = cssAsString.replace(result.urlItemRegExpG, function(item) {
				var match = result.urlItemRegExp.exec(item);
				var idx = +match[1];
				var urlItem = result.urlItems[idx];
				var url = resolve(urlItem.url);
				idx = url.indexOf("?#");
				if(idx < 0) idx = url.indexOf("#");
				var urlRequest;
				if(idx > 0) { // idx === 0 is catched by isUrlRequest
					// in cases like url('webfont.eot?#iefix')
					urlRequest = url.substr(0, idx);
					return "\" + require(" + loaderUtils.stringifyRequest(this, urlRequest) + ") + \"" +
							url.substr(idx);
				}
				urlRequest = url;
				return "\" + require(" + loaderUtils.stringifyRequest(this, urlRequest) + ") + \"";
			}.bind(this));
		}


		var exportJs = compileExports(result, importItemMatcher.bind(this), camelCaseKeys);
		if (exportJs) {
			exportJs = "exports.locals = " + exportJs + ";";
		}

		var moduleJs;
		if(query.sourceMap && result.map) {
			// add a SourceMap
			map = result.map;
			if(map.sources) {
				map.sources = map.sources.map(function(source) {
					return source.split("!").pop();
				}, this);
				map.sourceRoot = "";
			}
			map.file = map.file.split("!").pop();
			map = JSON.stringify(map);
			moduleJs = "exports.push([module.id, " + cssAsString + ", \"\", " + map + "]);";
		} else {
			moduleJs = "exports.push([module.id, " + cssAsString + ", \"\"]);";
		}

		// embed runtime
		callback(null, "exports = module.exports = require(" +
			loaderUtils.stringifyRequest(this, require.resolve("./css-base.js")) +
			")(" + query.sourceMap + ");\n" +
			"// imports\n" +
			importJs + "\n\n" +
			"// module\n" +
			moduleJs + "\n\n" +
			"// exports\n" +
			exportJs);
	}.bind(this));
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.css-loader.toString"></a>[function <span class="apidocSignatureSpan">css-loader.</span>toString ()](#apidoc.element.css-loader.toString)
- description and source-code
```javascript
toString = function () {
    return toString;
}
```
- example usage
```shell
...
   ]
}
'''

or

'''js
const cssText = require('./test.css').toString();

console.log(cssText);
'''

If there are SourceMaps, they will also be included in the result string.

### ImportLoaders
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)

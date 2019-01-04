# @touno-io/pdf

**Pure javascript cross-platform module to extract texts from PDFs.**

[![version](https://img.shields.io/npm/v/@touno-io/pdf.svg)](https://www.npmjs.org/package/@touno-io/pdf)
[![downloads](https://img.shields.io/npm/dt/@touno-io/pdf.svg)](https://www.npmjs.org/package/@touno-io/pdf)
[![node](https://img.shields.io/node/v/@touno-io/pdf.svg)](https://nodejs.org/)
[![status](https://gitlab.com/autokent/@touno-io/pdf/badges/master/pipeline.svg)](https://gitlab.com/autokent/@touno-io/pdf/pipelines)

## Similar Packages
* [pdf2json](https://www.npmjs.com/package/pdf2json) buggy, no support anymore, memory leak, throws non-catchable fatal errors
* [j-pdfjson](https://www.npmjs.com/package/j-pdfjson) fork of pdf2json
* [@touno-io/pdfr](https://github.com/dunso/@touno-io/pdf) buggy, no tests
* [pdfreader](https://www.npmjs.com/package/pdfreader) using pdf2json
* [pdf-extract](https://www.npmjs.com/package/pdf-extract) not cross-platform using xpdf

## Installation
`npm install @touno-io/pdf`
 
## Basic Usage - Local Files

```js
const fs = require('fs')
const pdf = require('@touno-io/pdf')

let dataBuffer = fs.readFileSync('path to PDF file...')

pdf(dataBuffer).then(function(data) {

	// number of pages
	console.log(data.numpages)
	// number of rendered pages
	console.log(data.numrender)
	// PDF info
	console.log(data.info)
	// PDF metadata
	console.log(data.metadata) 
	// PDF.js version
	// check https://mozilla.github.io/pdf.js/getting_started/
	console.log(data.version)
	// PDF text
	console.log(data.text) 
        
})
```

## Basic Usage - HTTP
You can use [crawler-request](https://www.npmjs.com/package/crawler-request) which uses the `@touno-io/pdf`

## Exception Handling

```js
const fs = require('fs')
const pdf = require('@touno-io/pdf')

let dataBuffer = fs.readFileSync('path to PDF file...')

pdf(dataBuffer).then(function(data) {
	// use data
})
.catch(function(error){
	// handle exceptions
})
```

## Extend
* v1.0.9 and above break pagerender callback [changelog](https://gitlab.com/autokent/@touno-io/pdf/blob/master/CHANGELOG)
* If you need another format like json, you can change page render behaviour with a callback
* Check out https://mozilla.github.io/pdf.js/

```js
// default render callback
function render_page(pageData) {
    //check documents https://mozilla.github.io/pdf.js/
    let render_options = {
        //replaces all occurrences of whitespace with standard spaces (0x20). The default value is `false`.
        normalizeWhitespace: false,
        //do not attempt to combine same line TextItem's. The default value is `false`.
        disableCombineTextItems: false
    }

    return pageData.getTextContent(render_options)
	.then(function(textContent) {
		let lastY, text = ''
		for (let item of textContent.items) {
			if (lastY == item.transform[5] || !lastY){
				text += item.str
			}  
			else{
				text += '\n' + item.str
			}    
			lastY = item.transform[5]
		}
		return text
	})
}

let options = {
		pagerender: render_page,
		pwd: 'password'
}

let dataBuffer = fs.readFileSync('path to PDF file...')

pdf(dataBuffer, options).then(function(data) {
	//use new format
})
```

## Options

```js
const DEFAULT_OPTIONS = {
	// internal page parser callback
	// you can set this option, if you need another format except raw text
	pagerender: render_page,
	
	// max page number to parse
	max: 0,
	
	//check https://mozilla.github.io/pdf.js/getting_started/
	version: 'v1.10.100'
}
```
### *pagerender* (callback)
If you need another format except raw text.  

### *max* (number)
Max number of page to parse. If the value is less than or equal to 0, parser renders all pages.  

### *version* (string, pdf.js version)
check [pdf.js](https://mozilla.github.io/pdf.js/getting_started/)

* `'default'`
* `'v1.10.100'`
* `'v2.0.550'`

>*default* version is *v1.10.100*   
>[mozilla.github.io/pdf.js](https://mozilla.github.io/pdf.js/getting_started/#download)

## Test
* `mocha` or `npm test`
* Check [test folder](https://gitlab.com/autokent/@touno-io/pdf/tree/master/test) and [quickstart.js](https://gitlab.com/autokent/@touno-io/pdf/blob/master/quickstart.js) for extra usages.

## License
[MIT licensed](https://gitlab.com/autokent/@touno-io/pdf/blob/master/LICENSE) and all it's dependencies are MIT or BSD licensed.

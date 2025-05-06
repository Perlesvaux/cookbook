---
layout: post
title:  "Unit Testing (Javascript)"
date:   2024-06-11 15:29:11 -0600
categories: unit testing
tags: unit testing js javascript framework
---
* Table of contents
{:toc}

## Installation
 Install nvm to handle node and npm
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash
```
 Initialize your project's package.json
```bash
npm init
```
... Add scripts and dependencies
```bash
npm pkg set 'type'='module' && npm pkg set 'scripts.test'='mocha' && npm install --save-dev mocha chai
```
 Run the test!
```bash
npm test
```

## Unit testing sample
Function example **src/functions.js**
```javascript
export function helloWorld(){
  return "Hello World! xD"
}
```

Test example **test/test.js**
```javascript
import assert from 'assert';
import {helloWorld} from '../src/functions.js';

describe("A simple string comparison", ()=>{
  it("String matches", ()=>{
    assert.deepEqual(helloWorld(), "Hello World! xD")
  }) 
})
```

## Assumed project structure
```
myProject/
├── package.json
├── src
│   └── functions.js
└── test
    └── test.js
```

---
layout: post
title:  "Unit Testing (PHP)"
date:   2024-06-11 21:23:01 -0600
categories: unit testing
tags: unit testing php framework
---
* Table of contents
{:toc}

## Installation

Let's install PHP through [XAMPP](https://www.apachefriends.org/es/download.html). If needed, add it to $PATH

```bash
echo "export PATH=/opt/lampp/bin:$PATH" >> ~/.bashrc
```

### Option 1

Install [Composer](https://getcomposer.org/download/) and require PHPUnit as a development dependency:

```bash
composer require --dev phpunit/phpunit
```
 ... Run the test! Run PHPUnit from the project's directory
```bash
./vendor/bin/phpunit test/test.php
```

### Option 2
 Download the PHPUnit PHAR file and move it to your [user-level]({{ site.baseurl }}{% post_url 2024-06-12-user-level-top-priority-scripts %}) scripts directory:
```bash
wget https://phar.phpunit.de/phpunit.phar && chmod +x phpunit.phar && sudo mv phpunit.phar ~/bin/phpunit
```
 ... Run the test! Run PHPUnit from [user-level]({{ site.baseurl }}{% post_url 2024-06-12-user-level-top-priority-scripts %}) scripts directory
```bash
phpunit test/test.php
```
## Unit testing sample
 Function example **src/functions.php**
```php
<?php
function helloWorld() {
    return "Hello, World!";
}
```
 Test example **test/test.php**
```php
<?php
use PHPUnit\Framework\TestCase;

class FunctionsTest extends TestCase
{
    public function testHelloWorld()
    {
        require 'src/functions.php';
        $this->assertEquals("Hello, World!", helloWorld());
    }
}
```

## Assumed project structure
```
myProject/
├── src
│   └── functions.php
└── test
    └── test.php
```

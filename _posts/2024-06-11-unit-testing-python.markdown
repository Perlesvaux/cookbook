---
layout: post
title:  "Unit Testing (Python)"
date:   2024-06-11 14:20:01 -0600
categories: unit testing
tags: unit testing python framework py
---
* Table of contents
{:toc}

## Unit testing sample
Function example functions.py
```python
#!/usr/bin/env python3
def hello_world():
    return "Hello world! =D"
```

Test example test.py (Standard library already includes unittest module)
```python
#!/usr/bin/env python3
import unittest
from functions import hello_world

class TestHelloWorld(unittest.TestCase):
    def test_string_match(self):
        self.assertEqual(hello_world(), "Hello world! =D")

if __name__ == '__main__':
    unittest.main()
```
Run the test!
```bash
python3 test.py
```
## Assumed project structure
```
myProject/
├── functions.py
└── test.py
```

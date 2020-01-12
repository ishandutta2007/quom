![logo](https://raw.githubusercontent.com/Viatorus/quom/master/artwork/logo_banner.png)

![Travis](https://travis-ci.org/Viatorus/quom.svg?branch=master)
[![PyPI](https://img.shields.io/pypi/v/quom.svg)](https://pypi.org/project/Quom/)


# Quom
Quom is a single header generator for C/C++ libraries.

## Installation

```
pip install quom
```

Only **Python 3.6+** is supported.

## How it works

Quom resolves all local includes starting with the main file of your library.

Afterwards, it tries to find the related source files and places them at the end of the main file
or at a specific stitch location if provided.

## How to use it

```
usage: quom [-h] [--stitch format] [--include_guard format] [--trim]
            input output

Single header generator for C/C++ libraries.

positional arguments:
  input                 Input file path of the main file.
  output                Output file path of the generated single header file.

optional arguments:
  -h, --help            show this help message and exit
  --stitch format, -s format
                        Format of the comment where the source files should be placed
                        (e.g. // ~> stitch <~). Default: None (at the end of the main file)
  --include_guard format, -g format
                        Regex format of the include guard. Default: None
  --trim, -t            Reduce continuous line breaks to one. Default: True
```

Take a look into the [examples folder](examples/) for more.

## Simple example

The project:

```
|-src/
|  |-foo.hpp
|  |-foo.cpp
|   -main.cpp
|-out/
    -main_gen.hpp
```

*foo.hpp*

```cpp
#pragma once

int foo();
```

*foo.cpp*

```cpp
#include "foo.hpp"

int foo() {
    return 0;
}
```

*main.cpp*

```cpp
#include "foo.hpp"

int main() {
    return foo();
}
```

The command:

```
quom src/main.hpp main_gen.hpp
```

*main_gen.hpp*

```cpp
int foo();

int main() {
    return foo();
}

int foo() {
    return 0;
}
```

## Advanced example

The project:

```
|-src/
|  |-foo.hpp
|  |-foo.cpp
|   -foobar.hpp
|-out/
    -foobar_gen.hpp
```

*foo.hpp*

```cpp
#pragma once

#ifndef FOOBAR_FOO_HPP
#endif FOOBAR_FOO_HPP

extern int foo; 

#endif
```

*foo.cpp*

```cpp
#include "foo.hpp"

int foo = 42;
```

*foobar.hpp*

```cpp
#pragma once

#ifndef FOOBAR_HPP
#endif FOOBAR_HPP

#include "foo.hpp"

#endif

#ifdef FOO_MAIN

// ~> stitch <~

#endif
```

The command:

```
quom src/foobar.hpp foobar_gen.hpp -s "~> stitch <~" -g FOOBAR_.+_HPP
```

*foobar_gen.hpp*

```cpp
#pragma once

#ifndef FOOBAR_HPP
#endif FOOBAR_HPP

extern int foo;

#endif

#ifdef FOO_MAIN

int foo = 42;

#endif
```

### <img src="img/perl.svg" class="nx-icon">Perl + <img src="img/webassembly.svg" class="nx-icon">WebAssembly

Let's write Perl bindings in WebAssembly!

Note:
Today I am going to talk about Perl and WebAssembly

---

### Graham Ollis

<div class="nx-hide-bullet">

* <img src="img/fastly.svg"  class="nx-icon"> Senior Software Engineer
* <img src="img/twitter.svg" class="nx-icon"> [@plicease](https://twitter.com/plicease)
* <img src="img/github.svg"  class="nx-icon"> [github.com/plicease](https://github.com/plicease)
* <img src="img/perl.svg"    class="nx-icon"> cpan: [PLICEASE](https://metacpan.org/author/PLICEASE)

</div>

Note:
My name is Graham Ollis,
I am a senior software engineer at Fastly
I am known on the internet as PLICEASE

---

<img src="img/fastly.svg"  class="nx-icon"> Fastly

Note:
(todo: you should work at fastly?)

---

<img src="img/perl.svg"    class="nx-icon"> Perl

Note:
In my spare time I work on a number of Perl related open source projects.

There are two you may have heard of (or possibly even used)

---

**Alien::Build**

Note:
First is the Alien family of modules under the Alien-Build banner that let you declare external (non-CPAN) depdendnecies for 
your Perl modules

---

**FFI::Platypus**

Note: 
and the Platypus project which is an alternative foriegn function interface for Perl.

Platypus has some advantages over Perl's native foreign function interface XS,
espeically when you are writing library bindings and not extending the the Perl programming
language itself

And we will touch on Platypus in this talk because it is a related although different technology

---

### What is WebAssembly?

**WebAssembly** (Wasm) is a _fast_, _safe_ and _portable_ bytecode that can be used by
web browers for performance critical situations where plain JavaScript is not appropriate

Note:
Today though, I want to talk to you about WebAssembly or "Wasm", which is a fast, safe and portable bytecode
that can be used by web browsers in performance critical situations where JavaScript is not fast enough.

I think the key to WebAssembly's success is that it works well with a number of existing technologies
and toolchains.

---

<img src="diagrams/unixcompile.png">

Note:
For example, in a traditional Unix environment you would use a C compiler like clang to compile C
code into an object file, which can then be linked into an executable or a library which you can
run from the bash prompt or call from another program.  For example you can call a dynamic library
from Perl using Platypus.

---

<img src="diagrams/webcompile.png">

Note:
It turns out modern clang supports WebAssembly as a backend so you can take the same sort of object file
and link it with the wasm linker and get a WebAssembly binary file.  The same file format is used for
both programs or libraries.  A program in fact is just a wasm file that happens to have a _start function.
So we can run a WebAssembly program using a command line runtime, or we can call into it from JavaScript
inside a web browser.

---

<img src="diagrams/rustcompile.png">

Note:
And you don't have to be a wiz with C either because there are some more modern languages like Rust or Go
that also support WebAssembly as a backend.  A lot of WebAssembly is written in Rust as it turns out.

---

### Why WebAssembly?

* Pretty fast
* Good tools
* Pretty safe
* Easy introspection

Note:
Why Wasm, and not JavaScript?  Well for CPU bound tasks it is much faster, and approaches the speed of natively compiled code.
For another reason, you can develop your web application in a mature compiled language like Rust or C ....
Finally, like JavaScript, WebAssembly lets you sandbox part of your application, so that you won't accdientally
(or intentionally) take down the end user's system.  Also WebAssembly modules are easy to intropsect, since the WebAssembly
runtime has to know it a lot of detail exactly what gets passed between the guest and host environment in order to keep
things safe.

---

### How WebAssembly?

Note:
Let's try some demos!

---

<img src="img/webperl.png">

**WebPerl** (https://webperl.zero-g.net) lets you script your web application with Perl
just like you might already be doing in JavaScript

Note:
One example of using WebAssembly in the browser is Perl itself.  The WebPerl project has the goal of allowing
developers to run Perl in the browser.  Since Perl itself is a C program, the WebPerl project has ported Perl to the
particular flavor of C supported by the compiler, and added some hooks to make it work in the browser's environment. 

---

<img src="img/webperl.png"><br>

<textarea class="perl-source" rows="10" cols="30">
#!perl
use strict;
use warnings;
use 5.010;
say "Hello from Perl!";
</textarea><textarea class="js-source" rows="10" cols="30" style="display: none">
// javascript
Perl.eval(`
  use strict;
  use warnings;
  use 5.010;
  say "Hello from JavaScript!";
  say "(via Perl)!";
`);
</textarea><textarea class="perl-output" rows="10" cols="60"></textarea>

Note:
WebPerl Demo!

1. use Perl to print to textarea
2. use JavaScript to print to textarea via Perl
3. use perl to access DOM (window.title)

---

**WebAssembly** can also be useful in server applications where the sandboxed nature of
the technology allows running untrusted code that could misbehave

Note:
WebAssembly can also be useful in server or command line applications where the sandboxed
nature of the technology running untrusted code that could misbehave

---

<img src="img/fastly.svg"  class="nx-icon"> Fastly

<img src="diagrams/fastly.png">

Note:
For example at Fastly, which is a content delivery network, we allow customers to provide
their own WebAssembly applications that are run on the edge.  This allows our customers
to respond more quickly to their customers, in some cases, without having to go back to
their backend servers.

Previously customers could use a domain speicifc language called VCL to write their own
custom logic at the edge, but the ability to use general purpose programming language
and well known toolchains gives our customers a lot of flexability.

---

<img src="diagrams/perlcompile.png">

Note:
Being a Perl programmer, I am also interested in using WebAssembly from Perl.  The same
tools can be used to create a WebAssembly binary for use in perl that we used earlier
to write browser based web applications.

---

<img src="img/perl.svg"    class="nx-icon"> Perl

* `Wasm::Wasmtime`
* `Wasm.pm`
* `App::plasm`

Note:
In persuit of this goal, I've written some modules to allow me to do that.

Wasm::Wasmtime is a low level library that lets you introspect and call WebAssembly code.

Wasm.pm is a higher level interface that makes it easy to call between Perl and WebAssembly

Finally plasm or PerL webASeMbly is a tool for poking around with and running WebAssembly binaries
from the command-line.

---

### How WebAssembly?

**Wasmtime** is a Rust library that implements a WebAssembly runtime

Note:
Wasmtime is a popular Rust library that implements a WebAssembly runtime.

---

**Wasmtime** has an FFI friendly C API, which means it can easily be embedded in any language with an FFI

Note:
Helpfully, Wasmtime has a very FFI friendly C API, which means that it can pretty easily be embedded in any modern
programming language.

---

**FFI::Platypus** is an FFI for Perl

Note:
Remember from before, that Platypus is an FFI for Perl

---

**Wasm::Wasmtime** is a set of Wasmtime bindings for Perl written using FFI::Platypus

Note:
So I wrote Wasm::Wasmtime using Platypus

---

<img src="diagrams/wasmtimemodel.png">

Note:
Here is a diagram of how some of the most important classes in wasmtime work.

1. The module is a compiled in-memory representation of a WebAssembly binary.
2. The module can contain a number of name/FuncType mappins.
3. A FuncType is just a function signature, so this tells us what the functions are called and how to call them
4. The module also has other useful objects that let us introspect the WebAseembly binary
5. The MemoryType tells us the minimum and maxiumum number of pages the WebAssembly requires.

1. A Wasmetime instance can be created from the module
2. Which we can use to call Func or function objects
3. And access the WebAssembly's memory with a data pointer and size of the region
4. Importantly, although we can poke around with Wasm's memory, WebAssembly cannot address memory outside of its own region

---

Convert WebAssembly Text to WebAssembly binary

```perl [1-3|3]
use Wasm::Wasmtime::Wat2Wasm;

my $wasm = wat2wasm('(module)');
```

Note:
Wasmtime also has a numnber of useful utiliy functions like wat2wasm that translates WebAssembly Text, the textual
representation of WebAssembly into WebAssembly binary which can be executed by the WebAssembly runtime.

1. This is the most simple WebAssembly module possible, one that doesn't do anything.

---

Call WebAssembly from Perl

```perl [1-14|4-9|12|13|14]
use Wasm::Wasmtime;
 
my $module = Wasm::Wasmtime::Module->new( wat => q{
  (module
   (func (export "add") (param i32 i32) (result i32)
     local.get 0
     local.get 1
     i32.add)
  )
});
 
my $instance = Wasm::Wasmtime::Instance->new($module);
my $add = $instance->exports->add;
say $add->call(1,2);  # 3
```

Note:
Lets look at some code that calls WebAssembly from Perl

1. The WebAssembly text is passed into the module constructor.  This example just has a function that adds to integers together.
This is very useful for examples, and for playing around with WebAssembly.  You can also pass WebAssembly binary in or a
filename pointing to the location of the WebAssembly binary.
2. We then instantiate the WebAssembly by creating an instance object.
3. We query the instance object for the add function
4. And now we can call it.  We pass in the values 1 and 2 and 3 is returned as you would expect.

---

Call Perl from WebAssembly

```perl [1-17|5-8|11-14|16-17|19]
use Wasm::Wasmtime;
 
my $s = Wasm::Wasmtime::Store->new;
my $module = Wasm::Wasmtime::Module->new($s, wat => q{
  (module
    (func $hello (import "" "hello"))
    (func (export "run") (call $hello))
  )
});
 
my $cb = Wasm::Wasmtime::Func->new(
  $s, [],[],
  sub { say "hello world!" },
);
 
my $instance = Wasm::Wasmtime::Instance
  ->new($module,[$cb]);

$instance->exports->run->call(); # hello world!
```

Note:
You can also call WebAssembly from Perl!

1. Again, we provide the WebAssembly text inline here.  This module has two functions, one is internal which calls the external
function that we will have to provide from Perl.
2. Next we create a Wasmtime func object
3. When we create the instnace object this time, we have to provide the callback
4. Finally we can call the WebAssembly run function which calls the Perl callack which prints out hello world.

---

`Wasm.pm`

```perl [1-13|7-10|5]
package MathStuff;
 
use Wasm
  -api => 0,
  -exporter => 'all',
  -wat => q{ (module
    (func (export "add") (param i32 i32) (result i32)
      local.get 0
      local.get 1
      i32.add)
  ) };
 
1;
```

Note:
That is a lot of work though, and most of the time you don't want or need to do that level of introspection.
That is what Wasm.pm is for.

Wasm.pm doesn't expose the Wasmtime interface at all.  That is intentional, just in case we decide to change the
implementaton later.  I prefer Wasmtime for the time being, but there are similar projects like Wasmer that might
make sense down the line.  It might even make sense to support multiple implementation and decide at runtime which one to use.

1. Again, we provide the WebAssembly text inline, but you could provide WebAssembly binary here, or an external WebAssembly
file.
2. As a convenience we use the exporter option to have all exported functions automatically exported to the calling module.
We could also say 'ok' here to have them imported on request too.  Under the covers the Perl Exporter module is used.  That is
an important detail I think.  Where possible I tried to make Wasm.pm use Perlish interfaces.

---

Call `MathStuff.pm`

```perl [1-3]
use MathStuff;

say add(1,2);  # 3
```

Note:
Now we can use our MathStuff module just like any other Perl module.  You don't even need to know or care what
language or binary format is being used under the covers.

---

Call Perl from Wasm via `Wasm.pm`

```perl [1-13|1-3|5-11|13]
sub hello {
  print "hello world!\n";
}
 
use Wasm
  -api => 0,
  -wat => q{ (module
    (func $hello (import "main" "hello"))
    (func (export "run") (call $hello))
  ) }
;
 
run();   # hello world!
```

Note:
You can of course call Perl from WebAssembly using the Wasm.pm interface.

1. Here we just define a Perl subroutine "hello"
2. The WebAssembly text asks to import hello from the main package.  Notice that the WebAssembly code doesn't need to know
or care what language the hello function is implemented in.
3. Finally we can call the WebAssembly run function which calls the Perl helo subroutine.

---

Zero-effort Wasm bindings with `Wasm::Hook`

```text [1-11|2-9|10|11]
$ mkdir -p src lib
$ cat > src/mathstuff.wat
(module
  (func (export "add") (param i32 i32) (result i32)
    local.get 0
    local.get 1
    i32.add)
)
^D
$ plasm wat src/mathstuff.wat
$ mv src/mathstuff.wasm lib/MathStuff.wasm
```

Note:
Honestly though, even that is too much work!  With Wasm::Hook we can reduce the boilerplate even more.

1. We write some WebAssembly text that will implement our module.  In this case we are using WebAssembly Text, but with the
right tools this could also be C, Rust or Go.
2. The plasm subcommand wat uses wat2wasm to generate the WebAssembly binary.
3. Next we move the WebAssembly binary output where you would normally expect to see the .pm for our module.

---

Calling zero-effort Wasm bindings with `Wasm::Hook`

```perl
use Wasm::Hook;
use MathStuff;

say add(1,2); # 3
```

Note:
But we don't need to a .pm file, because Wasm::Hook installs an @INC hook finds WebAsembly binary files and generates the
necessary boilerplate for us.

Nice thing is that we can now take the very same WebAssembly binary and use it on any platform supported by Wasmtime without
having to recompile our module.

---

### How WebAssembly + Python

```python
import wasmtime.loader
import MathStuff

print(add(1,2)); # 3
```

Note:
In fact we can reuse it in other langauges.  Python has a wasmtime.loader interface that lets us use the same module from
Python.

---

### How WebAssembly + Node.js

```javascript
import { add } from 'MathStuff.wasm';

console.log(add(1,2));  # 3
```

Note:
You can also do the same thing with Node.js.  Since the Wasmtime module class has all of the information that we need to
know in order to find and call WebAssembly functions we should be able to reuse this module in any langauge that has
sufficiently advanced Wasmtime bindings.

---

**App::plasm** is a CLI tool for WebAssembly binaries

Note:
plasm or PerL wASM is a command-line tool for webassembly binaries.

---

```c [1-9]
#include &lt;stdio.h>

int
main(int argc, char *argv[])
{
  printf("Hello World!\n");
  for(int i=1; i&lt;argc; i++)
    printf("argv[%d] = \"%s\"\n", i, argv[i]);
}
```

Note:
Given a very simple C program that prints a greeting and the command-line arguments passed to it

---

```
$ wacc hello.c -o hello.wasm
$ plasm run hello.wasm one two
Hello World!
argv[1] = "one"
argv[2] = "two"
```

Note:
We can compile the C into a WebAssembly binary and run it with the plasm run subcommand.  It works exactly as if it were a native C program.

---

```
$ plasm dump hello.wasm 
(module
  (func (import "wasi_snapshot_preview1" "proc_exit") (param i32))
  (func (import "wasi_snapshot_preview1" "args_sizes_get") (param i32 i32) (result i32))
  (func (import "wasi_snapshot_preview1" "args_get") (param i32 i32) (result i32))
  (func (import "wasi_snapshot_preview1" "fd_seek") (param i32 i64 i32 i32) (result i32))
  (func (import "wasi_snapshot_preview1" "fd_close") (param i32) (result i32))
  (func (import "wasi_snapshot_preview1" "fd_fdstat_get") (param i32 i32) (result i32))
  (func (import "wasi_snapshot_preview1" "fd_write") (param i32 i32 i32 i32) (result i32))
  (memory (export "memory") 2)
  (func (export "_start") )
)
```

Note:
We can also use the plasm dump subcommand to print out the interface for this WebAssembly binary.  As you might expect there is a _start
function, which is what makes it a program and not just a library.  There is also a memory export so that the host language can interact
with the WebAssembly's linear memory region.  This program also imports a number of functions that you might think would interact with
the operating system.  In this case you'd be right.  proc_exit implemnts the C exit function.  There are a copuple of argument processing
function that lets the program get the command-line arguments.  There are a number of functions with the fd_ prefix that do IO.

---

```
$ cat hello.c          
#include <stdio.h>

int
main() { }
$ wacc -o hello.wasm hello.c
$ plasm dump hello.wasm     
(module
  (func (import "wasi_snapshot_preview1" "proc_exit") (param i32))
  (memory (export "memory") 2)
  (func (export "_start") )
)
```

Note:
Also interesting is that if we have less complicated program that doesn't query the command-line, or do IO, we get a much shorter
list of imports.  That is because WebAssembly only generates code and interfaces for the objects that get used by the module.
This makes sense since you don't want to ship the entire C library to a web browser if you are only using bits of it.

All of these system functions are imported from wasi_snapshot_preview1, what is that?

---

**WebAssembly System Interface** (**WASI**) is a simple interface (ABI and API) designed by Mozilla.

Note:
The WebAssembly System Interface or WASI is a simple ABI and API designed by Mozilla.

---

**WASI** is portable to any platform

Note:
WASI is portable to any platform

---

**WASI** provides POSIX features like file I/O constrained by capability-based security.

Note:
It provides a POSIX features like file I/O.  System resources like file I/O can be configured by the WebAssembly runtime.

---

TODO: sandboxed WASI

---

### Related Tech

**XS**, **FFI** and Wasm are different types of Foreign Function interfaces

---

### Good/Bad of XS

<div class="nx-hide-bullet">

* &#9989; Native to Perl
* &#9989; Available everywhere Perl is
* &#9989; Great for extending Perl itself
* &#10060; Not applicable to other languages
* &#10060; Tedious for APIs with lots of functions
* &#10060; Requires a lot of reading:

</div>

---

perlxs

<iframe src="https://perldoc.perl.org/perlxs.html" style="width: 100%; height: 500px;"></iframe>

---

perlapi

<iframe src="https://perldoc.perl.org/perlapi.html" style="width: 100%; height: 500px;"></iframe>

---

perlguts

<iframe src="https://perldoc.perl.org/perlguts.html" style="width: 100%; height: 500px;"></iframe>

---

### Good/Bad of FFI

<div class="nx-hide-bullet">

* &#9989; FFI is available on all modern Perl platform
* &#9989; FFI is applicable to other languages (ruby, python, etc)
* &#10060; XS is tedious for APIs with lots of functions
* &#10060; C has terrible introspection 

</div>

---

**libclang** could potentially reduce the tedium of writing FFI bindings
by parsing C `.h` files for type and function declerations.

---

Even so, most C libraries will require at least *some* human intervention.

---

C has terrible introspection

```c [1-17|4-5|7|9|11-15]
#ifndef FOO_H
#define FOO_H

#define MODE1 1
#define MODE2 2

int add(int,int);

void print_string(const char *);

typedef strict {
  ...
} foo_t;

void process_list(foo_t *);

#endif
```

---

### Good/Bad of Wasm

<div class="nx-hide-bullet">

* &#10060; x86_64 and Arm 64 only
* &#10060; Binaries only for Linux, macOS and Windows
* &#9989; Wasm is applicable to lots of languages
 * Host: Perl / Python / Node.js
 * Guest: C / Rust / Go

</deiv>

---

### Good/Bad of Wasm

<div class="nx-hide-bullet">

* &#9989; Wasm *can* access the filesystem via WASI
* &#10060; Wasm does not typically have access to the network.
 * Porting SQLite or ImageMagik to Wasm is easy(ish)
 * Porting libcurl probably hard (for now)

</div>

---

### Good/Bad of Wasm

<div class="nx-hide-bullet">

* &#9989; Wasm bindings are almost effortless
* &#9989; Wasm has good introspection

</div>

---

### Good/Bad of Wasm

<div class="nx-hide-bullet">

* &#10060; Supported types: i32, i64, f32, f64<sup>*</sup>
* &#10060; Strings, arrays require some manual intervention<sup>*</sup>

</div>

<small style="margin-left: 60%; margin-top: 50px;"><sup>*</sup>Hopefully just until...</small>

---

Passing strings from Perl to WebAssembly<br>
(the C part)

```c [1-23|5|7-10|12-15|17-23|19|20|21|22]
#include &lt;stdlib.h>
#include &lt;stdio.h>
#include &lt;string.h>

#define EXPORT __attribute__ ((visibility ("default")))

EXPORT void *
_allocate(size_t size) {
  return malloc(size);
}

EXPORT void
_deallocate(void* ptr) {
  free(ptr);
}

EXPORT char *
_greet(const char *subject) {
  int len = strlen(subject) + strlen("Hello, ") + 1;
  char *greeting = malloc(len);
  snprintf(greeting, len, "Hello, %s", subject);
  return greeting;
}
```

---

Passing strings from Perl to WebAssembly<br>
(the Perl part)

```perl [1-31|19|20|22|24-27|29-30|32]
package Greet;

use strict;
use warnings;
use FFI::Platypus;
use FFI::Platypus::Memory qw( strcpy );
use base qw( Exporter );
use Wasm
  -api => 0,
  -self
;

our @EXPORT = qw( greet );

sub greet
{
  my($subject) = @_;

  my $input_offset = _allocate(length($subject) + 1);
  strcpy( $memory->address + $input_offset, $subject );

  my $output_offset = _greet($input_offset);

  my $greeting = FFI::Platypus->new->cast(
    'opaque', 'string', 
    $memory->address + $output_offset
  );

  _deallocate($input_offset);
  _deallocate($output_offset);

  return $greeting;
}

1;
```

---

Passing strings from Perl to WebAssembly<br>
(using it)


```perl [1-3]
use Greet;

say greet("Perl!");  # Hello, Perl!
```


---

WebAssembly **Interface Types** will allow host languages to call into WebAssembly pass strings other types without copies

---

This works because most languages store strings in the same way in linear memory

---

Objects can be stored as pointers...

---

Arrays should also be doable.

---

Passing strings from Perl to WebAssembly<br>
(aspirational)

```perl
use Wasm::Hook;
use Greet;

say greet("Perl!");  # Hello, Perl!
```

---

### Lucet

**Lucet** is a native WebAssembly compiler and runtime. It is designed to safely execute untrusted WebAssembly programs inside your application

---

<img src="diagrams/lucetcompile.png">

---

### Any Questions? 

<div class="nx-hide-bullet">

* IRC **#native** on **irc.perl.org**
* <img src="img/github.svg"  class="nx-icon"> [github.com/perlwasm](https://github.com/perlwasm)
* <img src="img/twitter.svg" class="nx-icon"> [@plicease](https://twitter.com/plicease)

</div>

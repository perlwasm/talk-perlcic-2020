### <img src="img/perl.svg" class="nx-icon">Perl + <img src="img/webassembly.svg" class="nx-icon">WebAssembly

Let's write Perl bindings in WebAssembly!

---

### Graham Ollis

<div class="nx-hide-bullet">

* <img src="img/fastly.svg"  class="nx-icon"> Senior Software Engineer
* <img src="img/twitter.svg" class="nx-icon"> [@plicease](https://twitter.com/plicease)
* <img src="img/github.svg"  class="nx-icon"> [github.com/plicease](https://github.com/plicease)
* <img src="img/perl.svg"    class="nx-icon"> cpan: [PLICEASE](https://metacpan.org/author/PLICEASE)

</div>

---

### What is WebAssembly?

**WebAssembly** (Wasm) is a _fast_, _safe_ and _portable_ bytecode that can be used by
web browers for performance critical situations where plain JavaScript is not appropriate.

---

<img src="diagrams/unixcompile.png">

---

<img src="diagrams/webcompile.png">

---

<img src="diagrams/rustcompile.png">

---

<img src="img/webperl.png">

[WebPerl](https://webperl.zero-g.net) lets you script your web application with Perl
just like you might already be doing in JavaScript.

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

---

### What is WebAssembly?

**WebAssembly** can also be useful in server applications where the sandboxed nature of
the technology allows running untrusted code that could misbehave.

---

<img src="diagrams/perlcompile.png">

---

### Why WebAssembly?

* Pretty fast
* Pretty safe
* Easy introspection

---

### How WebAssembly?

**Wasmtime** is a rust library that implements a WebAssembly runtime.

---

**Wasmtime** has an FFI friendly C API, which means it can easily be called from any language with a decent FFI.

---

<img src="diagrams/wasmtimemodel.png">

---

### Call Wasm from Perl

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

---

### Call Perl from Wasm

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

---

### Wasm.pm

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

---

### Wasm.pm

```perl [1-3]
use MathStuff;

say add(1,2);  # 3
```

---

### Wasm.pm

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

---

### Wasm::Hook

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

---

### Wasm::Hook

```perl
use Wasm::Hook;
use MathStuff;

say add(1,2); # 3
```

---

### How WebAssembly + Python

```python
import wasmtime.loader
import MathStuff

print(add(1,2)); # 3
```

---

### How WebAssembly + Node.js

```javascript
import { add } from 'MathStuff.wasm';

console.log(add(1,2));  # 3
```

---

### WebAssembly vs. XS

* ️✔️ XS is XS is available anywhere Perl is
* ❌ XS is not applicable to other languages
* ❌ XS requires a lot of reading:

---

### perlxs

<img src="img/perlxs.png" align="center">

---

### perlapi

<img src="img/perlapi.png" align="center">

---

### perlguts

<img src="img/perlguts.png" align="center">

---

### WebAssembly vs. FFI

* FFI is available most places Perl is
* FFI is applicable to other languages (ruby, python, etc)
* C has terrible introspection 

---

### WebAssembly Interface Types

---

### Lucet

**Lucet** is a native WebAssembly compiler and runtime. It is designed to safely execute untrusted WebAssembly programs inside your application.

---

<img src="diagrams/lucetcompile.png">

---

### Any Questions? 

<div class="nx-hide-bullet">

* IRC #native on irc.perl.org
* <img src="img/github.svg"  class="nx-icon"> [github.com/perlwasm](https://github.com/perlwasm)

</div>

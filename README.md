# Stuff that drives you nuts

Create two "compilers" `bar-cc`:

```sh
#! /bin/sh

exec "cc" "$@"
```

and `foo-cc`:

```sh
#! /bin/sh

exec "${CC:-cc}" "$@"
```

and make them executable with `chmod 755 bar-cc foo-cc`.


Why and how is it possible that there is any noticable difference in
`foo-cc` and `bar-cc` for **cmake** ?


Use this `CMakeLists.txt`:

```sh
project( crazy C)

```

Then `run` like this:

```sh
#! /bin/sh

PATH="${PWD}:$PATH"
unset CC

echo "bar-cc" >&2
echo >&2

[ -d build ] && rm -rf build

(
   mkdir build &&
   cd build &&
   cmake -DCMAKE_C_COMPILER=bar-cc ..
) || exit 1

[ -d build ] && rm -rf build

echo >&2
echo >&2
echo "foo-cc" >&2
echo >&2

(
   mkdir build &&
   cd build &&
   cmake -DCMAKE_C_COMPILER=foo-cc ..
) || exit 1

```

Now invoke **cmake** with

```console
cmake -DCMAKE_C_COMPILER=${PWD}/foo-cc .`
```

and it will hang (with `cmake version 3.18.4`).

It will not hang with `bar-cc`.


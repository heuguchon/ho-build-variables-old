# Overview
This repository is intended to centralize string data common to many builds
(such as required compiler switches), both internal to Linden and external, on
TeamCity and on individual dev machines.

# Bash Usage
Intended usage is to clone this repository as a sibling to the source
directory being built, then use the bash "source" or "." commands to read
../build-variables/convenience into your build script. This gives you
shell variables such as

LL_BUILD_RELEASE, LL_BUILD_RELWITHDEBINFO, LL_BUILD_DEBUG

which are aliases for the platform-specific variables (e.g.)

LL_BUILD_LINUX_RELEASE, LL_BUILD_LINUX_RELWITHDEBINFO, LL_BUILD_LINUX_DEBUG

as well as product-specific variables

VIEWER_BUILD_RELEASE, VIEWER_BUILD_RELWITHDEBINFO, VIEWER_BUILD_DEBUG

et al.

If you pass a build-type argument (Release, RelWithDebInfo, Debug) when
sourcing the convenience script, e.g.:

    source ../build-variables/convenience Release

then you get additional aliases:

LL_BUILD, VIEWER_BUILD et al.

which (in the example above, assuming you're running on Linux) are the same as
LL_BUILD_LINUX_RELEASE etc.

The convenience script also provides bash functions for removing or replacing
individual switches from any one of those variables. See the comments in the
script for function usage.

# Other Languages
## Variable Suite
The convenience script sources the variables script, which provides the
underlying suite of variables. Variable names are of the form:

{product}_BUILD_{platform}_{buildtype}

where:

<dl>
<dt>product</dt>
<dd>VIEWER, SERVER, other. LL for switches to apply to *all* products.</dd>
<dt>platform</dt>
<dd>WINDOWS, DARWIN, LINUX.</dd>
<dt>buildtype</dt>
<dd>RELEASE, RELWITHDEBINFO, DEBUG. BASE for switches common to all.</dd>
</dl>

These variables are in turn composed of variables that separate out
command-line macro definitions from other kinds of compiler switches:

{product}_BUILD_{platform}_{buildtype}_{category}

where:

<dl>
<dt>category</dt>
<dd>MACROS, SWITCHES.</dd>
</dl>

These are typically aggregated into the corresponding
{product}_BUILD_{platform}_{buildtype} variables:

LL_BUILD_LINUX_RELEASE="$LL_BUILD_LINUX_RELEASE_SWITCHES $LL_BUILD_LINUX_RELEASE_MACROS"

CMake, in particular, distinguishes between macro definitions
([add_definitions()](https://cmake.org/cmake/help/v3.1/command/add_definitions.html))
and other compiler switches
([add_compile_options()](https://cmake.org/cmake/help/v3.1/command/add_compile_options.html)),
so it may be useful to populate those CMake commands from the underlying
_MACROS and _SWITCHES variables.

## Parsing the variables file
The variables file should be kept as simple as possible to parse, so it can be
read from other languages than bash. Refer to the comments in that file itself
for syntax rules.

# Chrono

[![GitHub](https://shields.southcla.ws/badge/sampctl-pawn--chrono-2f2f2f.svg?style=for-the-badge)](https://github.com/Southclaws/pawn-chrono)

A modern Pawn library for working with dates and times.

## Installation

Simply install to your project:

```bash
sampctl package install Southclaws/pawn-chrono
```

Include in your code and begin using the library:

```pawn
#include <chrono>
```

## Usage

This library provides functions, constants string constants and functions for
working with times and dates.

### Tags

Tags are important, you should avoid passing around bare integers as timestamp
or duration values. Tagging your variables helps catch mistakes during
compilation.

These tags are also used by this library to provide useful conversion functions
between units of time.

* `Timestamp:` A tag that indicates a cell contains a Unix timestamp.
* `Milliseconds:` Cell contains milliseconds duration.
* `Seconds:` Cell contains seconds duration.
* `Minutes`: Cell contains minutes duration.
* `Hours`: Cell contains hours duration.
* `Days`: Cell contains days duration.
* `Weeks`: Cell contains weeks duration.
* `Months`: Cell contains months duration.
* `Years`: Cell contains years duration.

There also exists `TIME_UNITS` which is a macro for all time unit tags, this is
useful if you write a function that accepts any unit of time - remember to use
`tagof` for runtime checking!

### Constants

There are a set of constants that follow the naming convention:

`UnitInUnits`

This includes:

`SecondInMilliseconds`

Going all the way up to

`YearInDays`

For expressive conversions between units. For example, to get `5` minutes in
seconds, you can simple do:

```pawn
new Seconds:s = Seconds:(5 * _:MinuteInSeconds);
```

See the source code for all the unit constants.

### Natives

#### `Timestamp:Now()`

A tag-safe replacement for `gettime()`. Does not take arguments like `gettime`
does, always returns the current number of seconds since the Unix epoch.

#### `TimeFormat(Timestamp:ts, const fmt[], output[], len = sizeof output)`

A formatting function that takes a `Timestamp:` with a format string and outputs
a formatted time string using the standard C/++ specifiers (like `%Y` for
4-digit year, `%m` for month, etc.)

For example:

```pawn
new
    Timestamp:ts = Timestamp:1527929232,
    output[256];

TimeFormat(ts, WEEKDAY_NAME, output);
print(output);
```

Will print `Saturday`.

```pawn
new
    Timestamp:ts = Timestamp:1527929232,
    output[256];

TimeFormat(ts, MONTH_NAME, output);
print(output);
```

Will print `June`.

##### Templates

There are also a set of templates for standard formats:

* `HUMAN_DATE`: `31/05/18`
* `ISO6801_TIME`: `09:55:22`
* `ISO6801_DATE`: `2018-05-31`
* `ISO6801_FULL_UTC`: `2018-05-31T09:55:22Z`
* `ISO6801_FULL_LOCAL`: `2018-05-31T09:55:22`

For example, to create an ISO-8601 standard format date:

```pawn
new
    Timestamp:ts = Timestamp:1527929232,
    output[256];

TimeFormat(ts, ISO6801_FULL_UTC, output);
print(output);
```

Will print `2018-06-02T08:47:12Z`.

Because this is a standard format, it will easily be processed by most modern
programming languages and databases.

#### `TimeParse(const string[], const fmt[], &Timestamp:output)`

A parser for strings containing dates and times that uses the C/++ specifiers to
perform the reverse of `TimeFormat`.

For example:

```pawn
new Timestamp:ts, ret;
ret = TimeParse("2018-06-02T08:47:12Z", ISO6801_FULL_UTC, ts);
printf("%d", _:ts);
```

Will print `1527929232`.

#### `DurationParse(const string[], &Milliseconds:output)`

Parses duration strings and outputs their values in milliseconds, for example:

* `5ms` > `5`
* `5s` > `5000`
* `10m` > `600000`
* `3h` > `10800000`
* `1d` > `86400000`
* `8m5s` > `485000`
* `1d3h10m5s5ms` > `97805005`

### Operators

#### `Seconds:operator-(Timestamp:future, Timestamp:past)`

Casts timestamp difference to `Seconds:` for usage in duration-based functions.

#### `Timestamp:operator+(Timestamp:t, T:d)`

Where `T` is one of `Seconds`, `Minutes`, `Hours` or `Days`.

For example:

```pawn
new
    Timestamp:t = Timestamp:1527929232,
    Minutes:d = Minutes:5;

t += d;
// t is now 1527929532, aka advanced by 5 minutes, aka advanced by 300 seconds
```

Because `d` is tagged as `Minutes`, when it's added to `t` with `+=`, it doesn't
just add the integer `5`, it adds the seconds value of 5 minutes, which is 300.

## Testing

To run unit tests for Windows first build the plugin using Visual Studio, then:

```bash
make test-windows
```

To build and run tests for Linux on Windows, make sure you have Docker installed
and run:

```bash
make build-linux
make test-linux
```

This simply builds the plugin in a Docker container then tests it with sampctl
using the `--container` flag to run it in a Linux container.

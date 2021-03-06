# cachectl

`cachectl` is a controller for regular file's page cache. 

## Dependency

`posix_fadvise` is required.

## Installation

```
go get -u github.com/cubicdaiya/cachectl/...
```

## Show page cache stat for file

```
cachectl -f /var/log/access_log
```

## Purge page cache for file

```
cachectl -op purge -f /var/log/access_log
```

If you want to leave a cache appended recently, assigning a rate for purging page cache with `-r` is recommended.

```
cachectl -op purge -f /var/log/access_log -r 0.9
```

# cachectld

`cachectld` is a daemon for scheduled purging page cache. Its behavior is described by [TOML](https://github.com/toml-lang/toml).

```
cachectld -c conf/cachectld.toml
```

## Configuration for cachectld

A configuration for `cachectld` has one or multiple targets.

|name          |type  |description                                  |default|note                                             |
|--------------|------|---------------------------------------------|-------|-------------------------------------------------|
|path          |string|target file path                             |       |directory or file path                           |
|purge_interval|int   |interval for purging page cache for file     |3600   |unit is second. if -1 is set, purge does not run.|
|filter        |string|filtering pattern string for target file path|.*     |regular expression with golang's regexp package  |
|rate          |float |rate of purging page cache for file          |0.0    |0.0 and 1.0 is same behavior (0.0 <= rate <= 1.0)|

A example is below.

```toml
[[targets]]
path = "/vagrant/cachectl.go"
purge_interval = 30

[[targets]]
path = "/vagrant/cachectld.go"
purge_interval = 20

[[targets]]
path = "/vagrant/cachectl"
purge_interval = 5
filter = "\\.go$"
rate = 0.9
```

## Signal trigger

From `v0.3.0`, you can trigger with `SIGUSR1` to run the targets for `cachectld`.

```
$ pkill -USR1 cachectld
```

When `SIGUSR1` is received, `cachectld` runs all targets including the targets `purge_interval` is set -1.

## License

Copyright 2014-2016 Tatsuhiko Kubo


Licensed under the MIT License.

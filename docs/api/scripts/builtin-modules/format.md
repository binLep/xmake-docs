
# format

- Formatting a string

#### Function Prototype

::: tip API
```lua
format(formatstring: <string>, ...)
```
:::


#### Parameter Description

| Parameter | Description |
|-----------|-------------|
| formatstring | Format string |
| ... | Variable arguments for formatting |

#### Usage

If you just want to format the string and don't output it, you can use this interface. This interface is equivalent to the `string.format` interface,
just a simplified version of the interface name.

```lua
local s = format("hello %s", xmake)
```

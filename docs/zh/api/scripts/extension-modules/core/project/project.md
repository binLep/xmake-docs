# core.project.project

用于获取当前工程的一些描述信息，也就是在`xmake.lua`工程描述文件中定义的配置信息，例如：[target](/zh/api/description/project-target)、 [option](/zh/api/description/configuration-option) 等。

## project.target

- 获取指定工程目标对象

#### 函数原型

::: tip API
```lua
project.target(name: <string>)
```
:::

#### 参数说明

| 参数 | 描述 |
|------|------|
| name | 必需。目标名称 |

#### 返回值说明

| 类型 | 描述 |
|------|------|
| target | 返回目标对象，如果不存在则返回 nil |

#### 用法说明

获取和访问指定工程目标配置，例如：

```lua
local target = project.target("test")
if target then

    -- 获取目标名
    print(target:name())

    -- 获取目标目录, 2.1.9版本之后才有
    print(target:targetdir())

    -- 获取目标文件名
    print(target:targetfile())

    -- 获取目标类型，也就是：binary, static, shared
    print(target:targetkind())

    -- 获取目标名
    print(target:name())

    -- 获取目标源文件
    local sourcefiles = target:sourcefiles()

    -- 获取目标安装头文件列表
    local srcheaders, dstheaders = target:headerfiles()

    -- 获取目标依赖
    print(target:get("deps"))
end
```

## project.targets

- 获取工程目标对象列表

#### 函数原型

::: tip API
```lua
project.targets()
```
:::

#### 参数说明

此函数不需要参数。

#### 返回值说明

| 类型 | 描述 |
|------|------|
| table | 返回一个表，包含所有目标对象，键为目标名称，值为目标对象 |

#### 用法说明

返回当前工程的所有编译目标，例如：

```lua
for targetname, target in pairs(project.targets()) do
    print(target:targetfile())
end
```

## project.option

- 获取指定选项对象

#### 函数原型

::: tip API
```lua
project.option(name: <string>)
```
:::

#### 参数说明

| 参数 | 描述 |
|------|------|
| name | 必需。选项名称 |

#### 返回值说明

| 类型 | 描述 |
|------|------|
| option | 返回选项对象，如果不存在则返回 nil |

#### 用法说明

获取和访问工程中指定的选项对象，例如：

```lua
local option = project.option("test")
if option:enabled() then
    option:enable(false)
end
```

## project.options

- 获取工程所有选项对象

#### 函数原型

::: tip API
```lua
project.options()
```
:::

#### 参数说明

此函数不需要参数。

#### 返回值说明

| 类型 | 描述 |
|------|------|
| table | 返回一个表，包含所有选项对象，键为选项名称，值为选项对象 |

#### 用法说明

返回当前工程的所有编译目标，例如：

```lua
for optionname, option in pairs(project.options()) do
    print(option:enabled())
end
```

## project.name

- 获取当前工程名

#### 函数原型

::: tip API
```lua
project.name()
```
:::

#### 参数说明

此函数不需要参数。

#### 返回值说明

| 类型 | 描述 |
|------|------|
| string | 返回工程名称 |

#### 用法说明

也就是获取[set_project](/zh/api/description/global-interfaces#set-project)的工程名配置。

```lua
print(project.name())
```

## project.version

- 获取当前工程版本号

#### 函数原型

::: tip API
```lua
project.version()
```
:::

#### 参数说明

此函数不需要参数。

#### 返回值说明

| 类型 | 描述 |
|------|------|
| string | 返回工程版本号 |

#### 用法说明

也就是获取[set_version](/zh/api/description/global-interfaces#set-version)的工程版本配置。

```lua
print(project.version())
```


最右脚手架工具

### 安装

``` bash
$ npm install -g @xc/zuiyou-cli
```

### 使用

``` bash
$ zuiyou init <template-name> <project-name>
```

例如:

``` bash
$ zuiyou init webpack my-project
```

上面的命令会从 [zy-templates/webpack](https://github.com/zy-templates/webpack)拉取脚手架代码, 并提供一些交互, 将在 `./my-project/`目录下面生成项目.


### 官方脚手架
所有的脚手架可以到[zy-templates organization](https://github.com/zy-templates)看到. 你可以执行 `zuiyou init <template-name> <project-name>` 命令使用脚手架. 你可以使用`zuiyou list` 命令去查看所有的脚手架。

当前可以用的脚手架有:

- [webpack](https://github.com/zy-templates/webpack) - webpack


### 开发自己的脚手架

你可以简单的“复制”一份官方脚手架，fork一下。通过 `zuiyou-cli`进行下载：

``` bash
zuiyou init username/repo my-project
```

 `username/repo` 是你脚手架的uri。

将使用 [download-git-repo](https://github.com/flipxfx/download-git-repo)进行下载。 因此你也可以使用 `bitbucket:username/repo` 下载 Bitbucket 项目 ，使用 `username/repo#branch` 下载其它分支项目.


### 本地脚手架

你也可以在你本地开发一个脚手架

``` bash
zuiyou init ~/fs/path/to-custom-template my-project
```

### 如何自定义脚手架

- 脚手架必须有一个`template` 目录来包含整个模板文件。
- 脚手架也可以有一个 元数据 配置文件，文件名叫 `meta.js` 或者 `meta.json`。配置文件包含一些字段：
  - `prompts`: 用于收集用户选项数据;

  - `filters`: 朗读用于条件过滤文件来渲染.
  
  - `metalsmith`: 用于在链中添加自定义metalsmith插件.

  - `completeMessage`: 生成模板时要向用户显示的消息。您可以在此处添加自定义说明.

  - `complete`: 而不是使用`completeMessage`，您可以使用函数在生成模板时运行填充。

#### prompts

元数据文件中的“prompts”字段应该是包含用户提示的对象哈希。 对于每个条目，键是变量名称，值是 [Inquirer.js question object](https://github.com/SBoudrias/Inquirer.js/#question). 例如:

``` json
{
  "prompts": {
    "name": {
      "type": "string",
      "required": true,
      "message": "Project name"
    }
  }
}
```

完成所有提示后，`template`中的所有文件将使用[Handlebars]（http://handlebarsjs.com/）呈现，并以提示结果作为数据。

##### 有条件的提示

可以通过添加“when”字段来提示提示，该字段应该是使用从先前提示收集的数据评估的JavaScript表达式。 例如：

``` json
{
  "prompts": {
    "lint": {
      "type": "confirm",
      "message": "Use a linter?"
    },
    "lintConfig": {
      "when": "lint",
      "type": "list",
      "message": "Pick a lint config",
      "choices": [
        "standard",
        "airbnb",
        "none"
      ]
    }
  }
}
```

只有当用户对`lint`提示回答“是”时，才会触发“lintConfig”的提示。

##### 预先注册的 Handlebars Helpers

两个常用的Handlebars助手，`if_eq`和`unless_eq`是预先注册的：

``` handlebars
{{#if_eq lintConfig "airbnb"}};{{/if_eq}}
```

##### 自定义的 Handlebars Helpers

您可能希望使用元数据文件中的`helpers`属性注册其他Handlebars助手。
对象键是帮助程序名称：

``` js
module.exports = {
  helpers: {
    lowercase: str => str.toLowerCase()
  }
}
```
注册后，可以按如下方式使用：


``` handlebars
{{ lowercase name }}
```

#### File filters

 元数据文件中的`filters`字段应该是包含文件过滤规则的对象散列。 对于每个条目，键是[minimatch glob pattern]（https://github.com/isaacs/minimatch），该值是在提示答案数据的上下文中评估的JavaScript表达式。 例：

``` json
{
  "filters": {
    "test/**/*": "needTests"
  }
}
```

只有当用户对`needTests`的提示回答“是”时，才会生成`test`下的文件。

请注意，minimatch的`dot`选项设置为`true`，因此默认情况下glob模式也会匹配dotfiles。

#### Skip rendering

元数据文件中的`skipInterpolation`字段应该是[minimatch glob pattern]（https://github.com/isaacs/minimatch）。 匹配的文件应跳过渲染。 例：

``` json
{
  "skipInterpolation": "src/**/*.vue"
}
```

#### Metalsmith

`zuiyou-cli` 使用 [metalsmith](https://github.com/segmentio/metalsmith) 生成项目。

你可以自定义zuiyou-cli创建的metalsmith构建器以注册自定义插件。

```js
{
  "metalsmith": function (metalsmith, opts, helpers) {
    function customMetalsmithPlugin (files, metalsmith, done) {
      // Implement something really custom here.
      done(null, files)
    }
    
    metalsmith.use(customMetalsmithPlugin)
  }
}
```

如果您在询问问题之前需要挂起metalsmith，您可以使用带有`before`键的对象。

```js
{
  "metalsmith": {
    before: function (metalsmith, opts, helpers) {},
    after: function (metalsmith, opts, helpers) {}
  }
}
```

#### Additional data available in meta.{js,json}

- `destDirName` - 目标目录名称

```json
{
  "completeMessage": "To get started:\n\n  cd {{destDirName}}\n  npm install\n  npm run dev"
}
```

- `inPlace` - 生成模板到当前目录

```json
{
  "completeMessage": "{{#inPlace}}To get started:\n\n  npm install\n  npm run dev.{{else}}To get started:\n\n  cd {{destDirName}}\n  npm install\n  npm run dev.{{/inPlace}}"
}
```

### `complete` function

Arguments:

- `data`: 您可以在 `completeMessage`里面访问相同的数据:
  ```js
  {
    complete (data) {
      if (!data.inPlace) {
        console.log(`cd ${data.destDirName}`)
      }
    }
  }
  ```

- `helpers`: 一些其他帮助的模块.
  - `chalk`:  `chalk` 模块
  - `logger`: [内置的zuiyou-cli记录器](/lib/logger.js)
  - `files`: 生成的文件数组
  ```js
  {
    complete (data, {logger, chalk}) {
      if (!data.inPlace) {
        logger.log(`cd ${chalk.yellow(data.destDirName)}`)
      }
    }
  }
  ```

### 安装特定模板版本

`zuiyou-cli` 也可以安装指定版本，例如:

安装1.0版本

```
vue init 'webpack#1.0' mynewproject
```

_Note_: 由于`#`字符的特殊含义，在zsh shell上需要周围的引号。

### License

[MIT](http://opensource.org/licenses/MIT)

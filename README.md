
A simple CLI for scaffolding zuiyou projects.

### Installation

``` bash
$ npm install -g @xc/zuiyou-cli
```

### Usage

``` bash
$ zuiyou init <template-name> <project-name>
```

Example:

``` bash
$ zuiyou init webpack my-project
```

The above command pulls the template from [zy-templates/webpack](https://github.com/zy-templates/webpack), prompts for some information, and generates the project at `./my-project/`.


### Official Templates

All official project templates are repos in the [zy-templates organization](https://github.com/zy-templates). When a new template is added to the organization, you will be able to run `vue init <template-name> <project-name>` to use that template. You can also run `zuiyou list` to see all available official templates.

Current available templates include:

- [webpack](https://github.com/zy-templates/webpack) - A full-featured Webpack 


### Custom Templates

It's unlikely to make everyone happy with the official templates. You can simply fork an official template and then use it via `zuiyou-cli` with:

``` bash
zuiyou init username/repo my-project
```

Where `username/repo` is the GitHub repo shorthand for your fork.

The shorthand repo notation is passed to [download-git-repo](https://github.com/flipxfx/download-git-repo) so you can also use things like `bitbucket:username/repo` for a Bitbucket repo and `username/repo#branch` for tags or branches.

If you would like to download from a private repository use the `--clone` flag and the cli will use `git clone` so your SSH keys are used.

### Local Templates

Instead of a GitHub repo, you can also use a template on your local file system:

``` bash
zuiyou init ~/fs/path/to-custom-template my-project
```

### Writing Custom Templates from Scratch

- A template repo **must** have a `template` directory that holds the template files.

### License

[MIT](http://opensource.org/licenses/MIT)

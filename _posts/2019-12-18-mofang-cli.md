---
layout: post
title:  从零开始搭建前端脚手架cli
date:   2019-07-18 01:08:00 +0800
header-img: "img/post-bg-rwd.jpg"
author:     "Vison"
catalog: true
tags:
    - JavaScript
---


### 前言

日常开发中，我们都只专注在业务上的开发，拿起一套开箱即用的模板项目就直接开搞了。很多时候都没有思考过，
平时我们使用的脚手架里面到底做了什么，并且如果是自己来搭一套脚手架，应该怎么去搭呢？

### cli的搭建

在搭建脚手架cli之前，我们首先思考一下，一个cli，需要什么能力呢？答案是初始化能力，那么初始化的功能需要怎样去实现呢，
这里，我们先梳理一下思路。

#### 搭建思路

首先考虑一下整个脚手架的功能，参考主流脚手架vue-cli和create-react-app发现他们都有一些相同的功能。

* 问询功能 -- 询问项目名称、项目描述等等；

* 下载功能 -- 问询完毕后需要将模版下载到本地；

* 修改项目信息 -- 将第一步中的问询信息写入项目中；

* git初始化 -- 能够省去用户初始化git的操作；

* 安装依赖 -- npm install，用户需要做的我们都帮忙做掉；

#### 依赖分析

好了，为了实现上述思路，cli将会引入如下依赖去实现对应的功能：

```javascript
const program = require('commander');  // commander负责读取命令
const inquirer = require('inquirer');   // inquirer负责问询
const download = require('download-git-repo');   // download-git-repo负责下载对应模板项目的git仓库
const fse = require('fs-extra');   // fs-extra负责文件的复制
const memFs = require('mem-fs');
const editor = require('mem-fs-editor');   // mem-fs-editor负责模板的复制以及嵌入模板字符串，它需要依赖mem-fs
const { exec } = require('child_process');   // child_process负责执行命令行
```

当然除了上述必须的依赖外，为了更好的交互体验，还引入了如下依赖：

```javascript
const chalk = require('chalk');   // 改变命令行输出样式
const ora = require('ora');   // 一个优雅地命令行交互spinner
```

### 代码编写

有了清晰的思路就可以开始编码了

#### 目录结构

├── bin                   
|   ├── www             // 可执行文件
├── src                   
|   ├── constants.js    // 常量定义 
|   ├── create.js       // 命令文件
|   ├── main.js         // 程序入口
|   ├── project.js      // 主要逻辑代码  
|   └── utils.js        // 工具类      
└── package.json

#### 入口文件

脚手架肯定是要全局安装的，这时候就需要声明一个可执行文件，配置如下

* package.json

```javascript
{
  "name": "mofang-cli",
  "bin": {
    "mofang-cli": "./bin/www"
  }
}
```

* /bin/www

```javascript

#! /usr/bin/env node 
// 声明脚本在Node环境下执行

require('../src/main.js');

```
这样，main.js 相当于程序的入口

#### 逻辑编写

* 命令解析

```javascript
const program = require('commander');
const path = require('path');
const { getPackageVersion } = require('./utils');
const version = getPackageVersion()

const mapAction = { // 需要生成的指令数据
	create: {
		alias: 'c',
		description: 'create a project',
		examples: [
			'mofang-cli create <project-name>',
		],
	},
	'*': {
		alias: '',
		description: 'command not found',
		examples: [],
	},
};
Reflect.ownKeys(mapAction).forEach((action) => {
	program
		.command(action) // 命令名
		.alias(mapAction[action].alias) // 命令别名
		.description(mapAction[action].description) // 命令描述
		.action(() => { // 命令执行的操作
			if (action === '*') { // 命令不存在
				console.log(mapAction[action].description);
			} else {
				require(path.resolve(__dirname, action))(...process.argv.slice(3)); // 引入命令对应操作模块js文件
			}
		});
});

```
* 问询，收集项目信息

```javascript
const inquirer = require('inquirer');
const fse = require('fs-extra');
const download = require('download-git-repo');
const chalk = require('chalk');
const ora = require('ora');
const path = require('path');
const memFs = require('mem-fs');
const editor = require('mem-fs-editor');
const { exec } = require('child_process');
const { getDirFileName } = require('./utils');
const {
	INJECT_FILES,
	FRAMEWORKS,
	VUE_TEMPLATES,
	REACT_TEMPLATES,
	REACT_NATIVE_TEMPLATES
} = require('./constants');

function Project(options) {
	this.config = Object.assign({
		projectName: '',
		description: '',
		template: '',
		author: ''
	}, options);
	const store = memFs.create();
	this.memFsEditor = editor.create(store);
}

Project.prototype.create = function() {
	this.inquire()
		.then((answer) => {
			this.config = Object.assign(this.config, answer);
			this.selectTemplate()
				.then(answer2 => {
					this.config = Object.assign(this.config, answer2);
					this.generate();
				})
		});
};

Project.prototype.selectTemplate = function() {
	const prompts = [];
	prompts.push({
		name: 'template',
		type: 'list',
		message: `Please pick a template:`,
		choices: this.config.framework === 'Vue' ? VUE_TEMPLATES : (
			this.config.framework === 'React' ?  REACT_TEMPLATES: REACT_NATIVE_TEMPLATES)
	})
	return inquirer.prompt(prompts);
}

Project.prototype.inquire = function() {
	const prompts = [];
	const { projectName, description, author } = this.config;
	if (projectName === '') {
		prompts.push({
			type: 'input',
			name: 'projectName',
			message: 'Please input project name:',
			validate(input) {
				if (!input) {
					return 'Project name must not null';
				}
				if (fse.existsSync(input)) {
					return `The folder ${input} is exist, please change one`;
				}
				return true;
			}
		});
	} else if (fse.existsSync(projectName)) {
		prompts.push({
			type: 'input',
			name: 'projectName',
			message: `The folder ${projectName} is exist, please change one`,
			validate(input) {
				if (!input) {
					return 'Project name must not null';
				}
				if (fse.existsSync(input)) {
					return `The folder ${input} is exist, please change one`;
				}
				return true;
			}
		});
	}

	if (description === '') {
		prompts.push({
			type: 'input',
			name: 'description',
			message: 'Please input project desc'
		});
	}

	if (author === '') {
		prompts.push({
			type: 'input',
			name: 'author',
			message: 'Please input author'
		});
	}

	prompts.push({
		name: 'framework',
		type: 'list',
		message: `Please pick a framework:`,
		choices: FRAMEWORKS
	})

	return inquirer.prompt(prompts);
};

module.exports = Project;
```

* 下载模版

```javascript

Project.prototype.generate = function() {
	const { projectName, description, author, template } = this.config;
	const projectPath = path.join(process.cwd(), projectName);
	const downloadPath = path.join(projectPath, '__download__');

	const downloadSpinner = ora('🚀  Downloading template...');
	downloadSpinner.start();
	// 下载git repo
	download(template, downloadPath, { clone: true }, (err) => {
		if (err) {
			downloadSpinner.color = 'red';
			downloadSpinner.fail(err.message);
			downloadSpinner.fail(`please check your network, make sure can access to template ${template}`);
			fse.remove(downloadPath);
			fse.remove(projectPath);
			return;
		}

		downloadSpinner.color = 'green';
		downloadSpinner.succeed('🎉  Download Success');

		// 复制文件
		const copyFiles = getDirFileName(downloadPath);

		copyFiles.forEach((file) => {
			fse.copySync(path.join(downloadPath, file), path.join(projectPath, file));
			console.log(`${chalk.green('✔ ')}${chalk.grey(`Create: ${projectName}/${file}`)}`);
		});
	});
}
```

* 修改模版信息

```javascript
Project.prototype.injectTemplate = function(source, dest, data) {
	this.memFsEditor.writeJSON(dest, Object.assign({}, this.memFsEditor.readJSON(source), data))
}

INJECT_FILES.forEach((file) => {
			this.injectTemplate(path.join(downloadPath, file), path.join(projectName, file), {
				name: projectName,
				description,
				author
			});
		});
```
* 初始化git & 安装依赖

```javascript
exec('git init');

exec('npm install', (error, stdout, stderr) => {
					if (error) {
						installSpinner.color = 'red';
						installSpinner.fail(chalk.red('🔗 Dependency installed, please try again.'));
						console.log(error);
					} else {
						installSpinner.color = 'green';
						installSpinner.succeed('🎉 Dependency installed success.');
						console.log(`${stderr}${stdout}`);
						console.log(chalk.default(`🎉  Successfully created project ${chalk.yellow(projectName)}`));
						console.log(chalk.default(`👉  Get started with the following commands:`));
						console.log();
						console.log(chalk.cyan(` ${chalk.gray('$')} cd ${projectName}`));
						console.log(chalk.cyan(` ${chalk.gray('$')} npm start`));
					}
				})
```

### 调试

编码完成后，可以在项目目录下使用npm link命令进行本地连调, 无需发到线上

```javascript

$ sudo npm link

$ mofang-cli create my-project
```

### 发布

参考[npm发布项目](https://wenku.baidu.com/view/b25ff654f01dc281e53af089.html)

### 使用

![](/img/in-post/mofang-cli-usage.png)

### 参考资料

* [项目地址](https://github.com/mofang-cli/mofang-cli)

* [npm地址](https://www.npmjs.com/package/mofang-cli)


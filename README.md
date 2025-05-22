[toc]

# [官方文档](https://docs.github.com/zh/actions)

```yml
https://docs.github.com/zh/actions
https://docs.github.com/zh/actions
```



# 配置

```yml
# 在项目的.github目录下新建一个目录、目录名字为：workflows 在目录下新建一个.yml
mkdir -p .github/workflows
cd .github/workflows
touch wuge.yml
```

# 基本结构

```yml
name 自动构建的名字
on 触发条件
schedule: # 时间表
cron: '0 19 * * *' # 每天国际时间 19 点，北京时间凌晨 3 点执行(北京+8)
watch # 监视
type: started # 类型：点击了星标
env 环境变量
jobs 任务
build 工作的 id
run-on 工作运行的环境平台
if 工作运行的判断
steps 包含一系列任务步骤
name 子任务名
user 使用官方的一些库完成一些操作
run 运行脚本
id 运行 id
```

# 自动构建的名字

```yml
name: Makefile CI
```

# 自动触发工作流

单个事件触发

```yml
on: [push]
```

多个事件触发

```yml
on: [push,fork]
```

在 `branches` 和 `tags` 中定义的模式：当以下项出现 `push` 事件时，将运行以下工作流

```yml
on:
  push:
    # 指定下列 分支
    branches:
      - main
      - 'mona/octocat'
      - 'releases/**'
    # 指定下列 tags
    tags:
      - v2
      - v1.*
```

在 branches-ignore 和 tags-ignore 中定义的模式：当以下项出现 `push` 事件时，将不运行以下工作流

```yml
on:
  push:
    # 排除下列 分支
    branches-ignore:
      - 'mona/octocat'
      - 'releases/**-alpha'
    # 排除下列 tags
    tags-ignore:
      - v2
      - v1.*
```

不能在一个工作流中使用 `branches` 、`tags` 和 `branches-ignore`  `tags-ignore` 筛选同一事件



`paths` 筛选器来包含哪些路径。目录或其子目录中的内容更改了，此示例就会运行

```yml
on:
  push:
    paths:
      - '**.js'
```

`paths-ignore:` 筛选器来排除路径。目录或其子目录中的内容更改了，此示例也不会运行

```yaml
on:
  push:
    paths-ignore:
      - 'docs/**'
```

`release:`筛查器来排查版本。版本状态更改了运行	

```yml
on:
  release:
    types: [published]
```

```yml
- published    	-出版
- unpublished 	-未发表	 --不触发工作流
- created				-创建		  --不触发工作流 会自动另存为草稿
- edited				-编辑		 --不触发工作流 会自动另存为草稿
- deleted				-已删除   --不触发工作流 会自动另存为草稿
- prereleased		-预发行   --不触发工作流
- released			-释放			--不触发工作流
```

 `schedule` 事件，可以在计划的时间触发工作流。

```yaml
on:
  schedule:
    - cron: "15 4,5 * * *"
```

计划任务语法有五个字段，中间用空格分隔，每个字段代表一个时间单位。

```yml
┌───────────── 分 (0 - 59)
│ ┌───────────── 时 (0 - 23)
│ │ ┌───────────── 一个月中的第几天 (1 - 31)
│ │ │ ┌───────────── 月 (1-12 或JAN-DEC)
│ │ │ │ ┌───────────── 星期 (0-6 或 SUN-SAT)
│ │ │ │ │
│ │ │ │ │
│ │ │ │ │
* * * * *
```

你可在这五个字段中使用以下运算符：

| 运算符 | 说明         | 示例                                                         |
| ------ | ------------ | ------------------------------------------------------------ |
| *      | 任何值       | `15 * * * *` 在每天每小时的每个第 15 分钟运行。              |
| ，     | 值列表分隔符 | `2,10 4,5 * * *` 在每天第 4 和第 5 小时的第 2 和第 10 分钟运行。 |
| -      | 值的范围     | `30 4-6 * * *` 在第 4、5 和 6 小时的第 30 分钟运行。         |
| /      | 步骤值       | `20/15 * * * *` 在第 20 分钟到第 59 分钟每隔 15 分钟运行一次（第 20、35 和 50 分钟）。 |

# 手动触发工作流

使用 `workflow_dispatch` 事件时，你可以在浏览器指定传递到工作流的输入

```yml
on:
  workflow_dispatch:
    inputs:
      logLevel:
        description: 'Log level'
        required: true
        default: 'warning'
        type: choice
        options:
        - info
        - warning
        - debug
      tags:
        description: 'Test scenario tags'
        required: false
        type: boolean
      environment:
        description: 'Environment to run tests against'
        type: environment
        required: true

jobs:
  log-the-inputs:
    runs-on: ubuntu-latest
    steps:
      - run: |
          echo "Log level: $LEVEL"
          echo "Tags: $TAGS"
          echo "Environment: $ENVIRONMENT"
        env:
          LEVEL: ${{ inputs.logLevel }}
          TAGS: ${{ inputs.tags }}
          ENVIRONMENT: ${{ inputs.environment }}
```

还可以在从脚本运行工作流程时传递输入，或者使用 GitHub CLI。 例如：

```shell
gh workflow run run-tests.yml -f logLevel=warning -f tags=false -f environment=staging
```

| **虚拟机**           | **处理器 (CPU)** | **内存 (RAM)** | **存储 (SSD)** | **体系结构** | **工作流标签**                                               |
| -------------------- | ---------------- | -------------- | -------------- | ------------ | ------------------------------------------------------------ |
| Linux                | 4                | 16 GB          | 14 GB          | X64          | `ubuntu-latest`, `ubuntu-24.04`, `ubuntu-22.04`              |
| Windows              | 4                | 16 GB          | 14 GB          | x64          | `windows-latest`、`windows-2025`、`windows-2022`、`windows-2019` |
| Linux [公共预览版]   | 4                | 16 GB          | 14 GB          | arm64        | `ubuntu-24.04-arm`, `ubuntu-22.04-arm`                       |
| Windows [公共预览版] | 4                | 16 GB          | 14 GB          | arm64        | `windows-11-arm`                                             |
| macOS                | 4                | 14 GB          | 14 GB          | Intel        | `macos-13`                                                   |
| macOS                | 3 (M1)           | 7 GB           | 14 GB          | arm64        | `macos-latest`, `macos-14`, `macos-15`                       |

为了避免被其他人点击 Star 导致的不必要的麻烦，还需要在 work­flow 文件中加上 `if: github.event.repository.owner.id == github.event.sender.id` 字段，这样只有仓库所有者，也就是你自己点 Star 才有效。

```yml
jobs:
  build:
    runs-on: ubuntu-latest
    if: github.event.repository.owner.id == github.event.sender.id
```



# GitHub Actions 入门教程

[P3TERX](https://p3terx.com/author/1/) • 2019-11-11 • [7 评论](javascript:void(0);) • 30213 阅读

## 前言

Github Ac­tions 是 GitHub 推出的持续集成 (Con­tin­u­ous in­te­gra­tion，简称 CI) 服务，它提供了配置非常不错的虚拟服务器环境，基于它可以进行构建、测试、打包、部署项目。简单来讲就是将软件开发中的一些流程交给云服务器自动化处理，比方说开发者把代码 push 到 GitHub 后它会自动测试、编译、发布。有了持续集成服务开发者就可以专心于写代码，其它乱七八糟的事情就不用管了，这样可以大大提高开发效率。本篇文章将介绍 GitHub Ac­tions 的基本使用方法。

## 申请 Actions 使用权

GitHub Ac­tions 目前（2019 年 11 月 11 日）还处在 `Beta` 阶段，需要[申请](https://p3terx.com/go/aHR0cHM6Ly9naXRodWIuY29tL2ZlYXR1cmVzL2FjdGlvbnMvc2lnbnVw)才能使用，申请后在仓库主页就可以看到 `Actions` 按钮了。



[![img](https://imgcdn.p3terx.com/post/20191111201337.png#vwid=1052&vhei=619)](https://imgcdn.p3terx.com/post/20191111201337.png#vwid=1052&vhei=619)



## 基础概念

- **workflow** （工作流程）：持续集成一次运行的过程。
- **job** （任务）：一个 workflow 由一个或多个 job 构成，含义是一次持续集成的运行，可以完成多个任务。
- **step**（步骤）：每个 job 由多个 step 构成，一步步完成。
- **action** （动作）：每个 step 可以依次执行一个或多个命令（action）。

## 虚拟环境

GitHub Ac­tions 为每个任务 (job) 都提供了一个虚拟机来执行，每台虚拟机都有相同的硬件资源：

- 2-core CPU
- 7 GB RAM 内存
- 14 GB SSD 硬盘空间

> 实测硬盘总容量为90G左右，可用空间为30G左右，评测详见：《[GitHub Actions 虚拟服务器环境简单评测](https://p3terx.com/archives/github-actions-virtual-environment-simple-evaluation.html)》

使用限制：

- 每个仓库只能同时支持20个 workflow 并行。
- 每小时可以调用1000次 GitHub API 。
- 每个 job 最多可以执行6个小时。
- 免费版的用户最大支持20个 job 并发执行，macOS 最大只支持5个。
- 私有仓库每月累计使用时间为2000分钟，超过后$ 0.008/分钟，公共仓库则无限制。

操作系统方面可选择 Win­dows server、Linux、ma­cOS，并预装了大量[软件包和工具](https://p3terx.com/go/aHR0cHM6Ly9oZWxwLmdpdGh1Yi5jb20vY24vYWN0aW9ucy9hdXRvbWF0aW5nLXlvdXItd29ya2Zsb3ctd2l0aC1naXRodWItYWN0aW9ucy9zb2Z0d2FyZS1pbnN0YWxsZWQtb24tZ2l0aHViLWhvc3RlZC1ydW5uZXJz)。

> **TIPS：** 虽然名称叫持续集成，但当所有任务终止和完成时，虚拟环境内的数据会随之清空，并不会持续。即每个新任务都是一个全新的虚拟环境。

## workflow 文件

GitHub Ac­tions 的配置文件叫做 work­flow 文件（官方中文翻译为 “工作流程文件”），存放在代码仓库的`.github/workflows` 目录中。work­flow 文件采用 YAML 格式，文件名可以任意取，但是后缀名统一为`.yml`，比如 `p3terx.yml`。一个库可以有多个 work­flow 文件，GitHub 只要发现`.github/workflows` 目录里面有`.yml` 文件，就会按照文件中所指定的触发条件在符合条件时自动运行该文件中的工作流程。在 Ac­tions 页面可以看到很多种语言的 work­flow 文件的模版，可以用于简单的构建与测试。



[![img](https://imgcdn.p3terx.com/post/20191112182333.png#vwid=1333&vhei=897)](https://imgcdn.p3terx.com/post/20191112182333.png#vwid=1333&vhei=897)



下面是一个简单的 work­flow 文件示例：

```yaml
name: Hello World
on: push
jobs:
  my_first_job:
    name: My first job
    runs-on: ubuntu-latest
    steps:
    - name: checkout
      uses: actions/checkout@master
    - name: Run a single-line script
      run: echo "Hello World!"
  my_second_job:
    name: My second job
    runs-on: macos-latest
    steps:
    - name: Run a multi-line script
      env:
        MY_VAR: Hello World!
        MY_NAME: P3TERX
      run: |
        echo $MY_VAR
        echo My name is $MY_NAME
```

## workflow 语法

> **TIPS：** 参照上面的示例阅读。

### name

`name` 字段是 work­flow 的名称。若忽略此字段，则默认会设置为 work­flow 文件名。

### on

`on` 字段指定 work­flow 的触发条件，通常是某些事件，比如示例中的触发事件是 `push`，即在代码 `push` 到仓库后被触发。`on` 字段也可以是事件的数组，多种事件触发，比如在 `push` 或 `pull_request` 时触发：

```yaml
on: [push, pull_request]
```

完整的事件列表，请查看[官方文档](https://p3terx.com/go/aHR0cHM6Ly9oZWxwLmdpdGh1Yi5jb20vY24vYWN0aW9ucy9hdXRvbWF0aW5nLXlvdXItd29ya2Zsb3ctd2l0aC1naXRodWItYWN0aW9ucy93b3JrZmxvdy1zeW50YXgtZm9yLWdpdGh1Yi1hY3Rpb25z)。下面是一些比较常见的事件：

<details open="" style="box-sizing: border-box; display: block; color: rgba(0, 0, 0, 0.86); font-family: &quot;Open Sans&quot;, &quot;PingFang SC&quot;, &quot;Hiragino Sans GB&quot;, &quot;Microsoft Yahei&quot;, &quot;WenQuanYi Micro Hei&quot;, &quot;Segoe UI Emoji&quot;, &quot;Segoe UI Symbol&quot;, Helvetica, Arial, -apple-system, system-ui, sans-serif; font-size: 18px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; white-space: normal; background-color: rgb(255, 255, 255); text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><slot id="details-content" pseudo="details-content" style="display: block;"><p style="box-sizing: border-box; margin: 0.92em 0px;">push 指定分支触发</p><pre class=" language-yaml line-numbers" style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 0.96em; position: relative; width: 820px; overflow: hidden; line-height: 1.5; border: 1px solid rgba(0, 0, 0, 0.05); border-radius: 5px; background: rgb(241, 243, 243); text-align: left; white-space: pre; word-spacing: normal; word-break: normal; overflow-wrap: normal; tab-size: 4; hyphens: none; counter-reset: linenumber 0;"><code class=" language-yaml" style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: 0px 0px !important; padding: 10px 10px 10px calc(3em + 10px); border-radius: 3px; text-align: left; white-space: inherit; word-spacing: normal; word-break: normal; overflow-wrap: normal; tab-size: 4; hyphens: none; display: block; overflow: auto;"><span class="token key atrule" style="box-sizing: border-box; color: rgb(0, 119, 170);">on</span><span class="token punctuation" style="box-sizing: border-box; color: rgb(153, 153, 153);">:</span>
  <span class="token key atrule" style="box-sizing: border-box; color: rgb(0, 119, 170);">push</span><span class="token punctuation" style="box-sizing: border-box; color: rgb(153, 153, 153);">:</span>
    <span class="token key atrule" style="box-sizing: border-box; color: rgb(0, 119, 170);">branches</span><span class="token punctuation" style="box-sizing: border-box; color: rgb(153, 153, 153);">:</span>
      <span class="token punctuation" style="box-sizing: border-box; color: rgb(153, 153, 153);">-</span> master</code><span aria-hidden="true" class="line-numbers-rows" style="box-sizing: border-box; position: absolute; pointer-events: none; top: 10px; font-size: 17.28px; left: 0px; width: 3em; letter-spacing: -1px; border-right: 1px solid rgba(153, 153, 153, 0.2); user-select: none; background: rgb(241, 243, 243);"><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span></span></pre><p style="box-sizing: border-box; margin: 0.92em 0px;">push tag 时触发</p><pre class=" language-yaml line-numbers" style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 0.96em; position: relative; width: 820px; overflow: hidden; line-height: 1.5; border: 1px solid rgba(0, 0, 0, 0.05); border-radius: 5px; background: rgb(241, 243, 243); text-align: left; white-space: pre; word-spacing: normal; word-break: normal; overflow-wrap: normal; tab-size: 4; hyphens: none; counter-reset: linenumber 0;"><code class=" language-yaml" style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: 0px 0px !important; padding: 10px 10px 10px calc(3em + 10px); border-radius: 3px; text-align: left; white-space: inherit; word-spacing: normal; word-break: normal; overflow-wrap: normal; tab-size: 4; hyphens: none; display: block; overflow: auto;"><span class="token key atrule" style="box-sizing: border-box; color: rgb(0, 119, 170);">on</span><span class="token punctuation" style="box-sizing: border-box; color: rgb(153, 153, 153);">:</span>
  <span class="token key atrule" style="box-sizing: border-box; color: rgb(0, 119, 170);">push</span><span class="token punctuation" style="box-sizing: border-box; color: rgb(153, 153, 153);">:</span>
    <span class="token key atrule" style="box-sizing: border-box; color: rgb(0, 119, 170);">tags</span><span class="token punctuation" style="box-sizing: border-box; color: rgb(153, 153, 153);">:</span>
    <span class="token punctuation" style="box-sizing: border-box; color: rgb(153, 153, 153);">-</span> <span class="token string" style="box-sizing: border-box; color: rgb(102, 153, 0);">'v*'</span></code><span aria-hidden="true" class="line-numbers-rows" style="box-sizing: border-box; position: absolute; pointer-events: none; top: 10px; font-size: 17.28px; left: 0px; width: 3em; letter-spacing: -1px; border-right: 1px solid rgba(153, 153, 153, 0.2); user-select: none; background: rgb(241, 243, 243);"><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span></span></pre><p style="box-sizing: border-box; margin: 0.92em 0px;">定时触发</p><pre class=" language-yaml line-numbers" style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 0.96em; position: relative; width: 820px; overflow: hidden; line-height: 1.5; border: 1px solid rgba(0, 0, 0, 0.05); border-radius: 5px; background: rgb(241, 243, 243); text-align: left; white-space: pre; word-spacing: normal; word-break: normal; overflow-wrap: normal; tab-size: 4; hyphens: none; counter-reset: linenumber 0;"><code class=" language-yaml" style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: 0px 0px !important; padding: 10px 10px 10px calc(3em + 10px); border-radius: 3px; text-align: left; white-space: inherit; word-spacing: normal; word-break: normal; overflow-wrap: normal; tab-size: 4; hyphens: none; display: block; overflow: auto;"><span class="token key atrule" style="box-sizing: border-box; color: rgb(0, 119, 170);">schedule</span><span class="token punctuation" style="box-sizing: border-box; color: rgb(153, 153, 153);">:</span>
  <span class="token punctuation" style="box-sizing: border-box; color: rgb(153, 153, 153);">-</span> <span class="token key atrule" style="box-sizing: border-box; color: rgb(0, 119, 170);">cron</span><span class="token punctuation" style="box-sizing: border-box; color: rgb(153, 153, 153);">:</span> 0 */6 * * *</code><span aria-hidden="true" class="line-numbers-rows" style="box-sizing: border-box; position: absolute; pointer-events: none; top: 10px; font-size: 17.28px; left: 0px; width: 3em; letter-spacing: -1px; border-right: 1px solid rgba(153, 153, 153, 0.2); user-select: none; background: rgb(241, 243, 243);"><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span></span></pre><p style="box-sizing: border-box; margin: 0.92em 0px;">发布 re­lease 触发</p><pre class=" language-yaml line-numbers" style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 0.96em; position: relative; width: 820px; overflow: hidden; line-height: 1.5; border: 1px solid rgba(0, 0, 0, 0.05); border-radius: 5px; background: rgb(241, 243, 243); text-align: left; white-space: pre; word-spacing: normal; word-break: normal; overflow-wrap: normal; tab-size: 4; hyphens: none; counter-reset: linenumber 0;"><code class=" language-yaml" style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: 0px 0px !important; padding: 10px 10px 10px calc(3em + 10px); border-radius: 3px; text-align: left; white-space: inherit; word-spacing: normal; word-break: normal; overflow-wrap: normal; tab-size: 4; hyphens: none; display: block; overflow: auto;"><span class="token key atrule" style="box-sizing: border-box; color: rgb(0, 119, 170);">on</span><span class="token punctuation" style="box-sizing: border-box; color: rgb(153, 153, 153);">:</span>
  <span class="token key atrule" style="box-sizing: border-box; color: rgb(0, 119, 170);">release</span><span class="token punctuation" style="box-sizing: border-box; color: rgb(153, 153, 153);">:</span>
    <span class="token key atrule" style="box-sizing: border-box; color: rgb(0, 119, 170);">types</span><span class="token punctuation" style="box-sizing: border-box; color: rgb(153, 153, 153);">:</span> <span class="token punctuation" style="box-sizing: border-box; color: rgb(153, 153, 153);">[</span>published<span class="token punctuation" style="box-sizing: border-box; color: rgb(153, 153, 153);">]</span></code><span aria-hidden="true" class="line-numbers-rows" style="box-sizing: border-box; position: absolute; pointer-events: none; top: 10px; font-size: 17.28px; left: 0px; width: 3em; letter-spacing: -1px; border-right: 1px solid rgba(153, 153, 153, 0.2); user-select: none; background: rgb(241, 243, 243);"><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span></span></pre><p style="box-sizing: border-box; margin: 0.92em 0px;">仓库被 star 时触发</p><pre class=" language-yaml line-numbers" style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 0.96em; position: relative; width: 820px; overflow: hidden; line-height: 1.5; border: 1px solid rgba(0, 0, 0, 0.05); border-radius: 5px; background: rgb(241, 243, 243); text-align: left; white-space: pre; word-spacing: normal; word-break: normal; overflow-wrap: normal; tab-size: 4; hyphens: none; counter-reset: linenumber 0;"><code class=" language-yaml" style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: 0px 0px !important; padding: 10px 10px 10px calc(3em + 10px); border-radius: 3px; text-align: left; white-space: inherit; word-spacing: normal; word-break: normal; overflow-wrap: normal; tab-size: 4; hyphens: none; display: block; overflow: auto;"><span class="token key atrule" style="box-sizing: border-box; color: rgb(0, 119, 170);">on</span><span class="token punctuation" style="box-sizing: border-box; color: rgb(153, 153, 153);">:</span>
  <span class="token key atrule" style="box-sizing: border-box; color: rgb(0, 119, 170);">watch</span><span class="token punctuation" style="box-sizing: border-box; color: rgb(153, 153, 153);">:</span>
    <span class="token key atrule" style="box-sizing: border-box; color: rgb(0, 119, 170);">types</span><span class="token punctuation" style="box-sizing: border-box; color: rgb(153, 153, 153);">:</span> <span class="token punctuation" style="box-sizing: border-box; color: rgb(153, 153, 153);">[</span>started<span class="token punctuation" style="box-sizing: border-box; color: rgb(153, 153, 153);">]</span></code><span aria-hidden="true" class="line-numbers-rows" style="box-sizing: border-box; position: absolute; pointer-events: none; top: 10px; font-size: 17.28px; left: 0px; width: 3em; letter-spacing: -1px; border-right: 1px solid rgba(153, 153, 153, 0.2); user-select: none; background: rgb(241, 243, 243);"><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span></span></pre><p style="box-sizing: border-box; margin: 0.92em 0px;"></p></slot></details>



### jobs

`jobs` 表示要执行的一项或多项任务。每一项任务必须关联一个 ID (`job_id`)，比如示例中的 `my_first_job` 和 `my_second_job`。`job_id` 里面的 `name` 字段是任务的名称。`job_id` 不能有空格，只能使用数字、英文字母和 `-` 或`_`符号，而 `name` 可以随意，若忽略 `name` 字段，则默认会设置为 `job_id`。

当有多个任务时，可以指定任务的依赖关系，即运行顺序，否则是同时运行。

```yaml
jobs:
  job1:
  job2:
    needs: job1
  job3:
    needs: [job1, job2]
```

上面代码中，`job1` 必须先于 `job2` 完成，而 `job3` 等待 `job1` 和 `job2` 的完成才能运行。因此，这个 work­flow 的运行顺序依次为：`job1`、`job2`、`job3`。

### runs-on

`runs-on` 字段指定任务运行所需要的虚拟服务器环境，是必填字段，目前可用的虚拟机如下：

> **TIPS：** 每个任务的虚拟环境都是独立的。

| 虚拟环境               | YAML workflow 标签                |
| :--------------------- | :-------------------------------- |
| Windows Server 2019    | `windows-latest`                  |
| Ubuntu 18.04           | `ubuntu-latest` or `ubuntu-18.04` |
| Ubuntu 16.04           | `ubuntu-16.04`                    |
| macOS X Catalina 10.15 | `macos-latest`                    |

### steps

`steps` 字段指定每个任务的运行步骤，可以包含一个或多个步骤。步骤开头使用 `-` 符号。每个步骤可以指定以下字段:

- `name`：步骤名称。
- `uses`：该步骤引用的`action`或 Docker 镜像。
- `run`：该步骤运行的 bash 命令。
- `env`：该步骤所需的环境变量。

其中 `uses` 和 `run` 是必填字段，每个步骤只能有其一。同样名称也是可以忽略的。

## action

`action` 是 GitHub Ac­tions 中的重要组成部分，这点从名称中就可以看出，`actions` 是 `action` 的复数形式。它是已经编写好的步骤脚本，存放在 GitHub 仓库中。

对于初学者来说可以直接引用其它开发者已经写好的 `action`，可以在[官方 action 仓库](https://p3terx.com/go/aHR0cHM6Ly9naXRodWIuY29tL2FjdGlvbnM)或者 [GitHub Marketplace](https://p3terx.com/go/aHR0cHM6Ly9naXRodWIuY29tL21hcmtldHBsYWNlP3R5cGU9YWN0aW9ucw) 去获取。此外 [Awesome Actions](https://p3terx.com/go/aHR0cHM6Ly9naXRodWIuY29tL3NkcmFzL2F3ZXNvbWUtYWN0aW9ucw) 这个项目收集了很多非常不错的 `action`。

既然 `action` 是代码仓库，当然就有版本的概念。引用某个具体版本的 `action`：

```yaml
steps:
  - uses: actions/setup-node@74bc508 # 指定一个 commit
  - uses: actions/setup-node@v1.2    # 指定一个 tag
  - uses: actions/setup-node@master  # 指定一个分支
```

一般来说 `action` 的开发者会说明建议使用的版本。

## 实例：编译 OpenWrt

最近一直在研究 Open­Wrt ，那就写个编译 Open­Wrt 的实例吧。

既然是编译 Open­Wrt 那么 work­flow 的名称就叫 `Build OpenWrt`

```yaml
name: Build OpenWrt
```

触发事件我选择了 push 。

```yaml
on: push
```

我个人常用的 Open­Wrt 编译环境使用的是 Ubuntu 18.04 ，所以任务所使用的虚拟环境也一样。

```yaml
jobs:
  build:
    runs-on: ubuntu-18.04
    steps:
```

我并不确定系统中是否有编译所需要依赖，所以第一个步骤是安装依赖软件包。

```yaml
- name: Installation depends
  run: |
    sudo apt-get update
    sudo apt-get -y install build-essential asciidoc binutils bzip2 gawk gettext git libncurses5-dev libz-dev patch unzip zlib1g-dev lib32gcc1 libc6-dev-i386 subversion flex uglifyjs git-core gcc-multilib p7zip p7zip-full msmtp libssl-dev texinfo libglib2.0-dev xmlto qemu-utils upx libelf-dev autoconf automake libtool autopoint
```

由于我使用的是一个空仓库，所以第二个步骤使用 Git 去拉取 Open­Wrt 官方源码。

```yaml
- name: Clone source code
  run: |
    git clone https://github.com/openwrt/openwrt
```

> **TIPS：** 如果是有源码的仓库，可以引用 [actions/checkout](https://p3terx.com/go/aHR0cHM6Ly9naXRodWIuY29tL2FjdGlvbnMvY2hlY2tvdXQ) 这个官方 ac­tion 把源码签出到工作目录中。工作目录也就是在 Ac­tions 中执行命令的根目录，其绝对路径为`/home/runner/work/REPO_NAME/REPO_NAME`，环境变量为`$GITHUB_WORKSPACE`。

然后还需要拉取 feeds ，它是扩展软件包源码，所以需要单独拉取。既然都是拉取源码，所以就都放在一起吧。

```yaml
- name: Clone source code
  run: |
    git clone https://github.com/openwrt/openwrt
    cd openwrt
    ./scripts/feeds update -a
    ./scripts/feeds install -a
```

由于这只是尝试，所以第三个步骤就让它生成一个默认的配置文件。由于每个步骤都会回退到工作目录，所以前面还需要加一条进入 `buildroot` 的命令。

```yaml
- name: Generate config file
  run: |
    cd openwrt
    make defconfig
```

第四个步骤是下载第三方软件包（俗称 dl 库），最后为了防止下载不完整导致编译失败，加了显示不完整文件和删除不完整文件的命令。

```yaml
- name: Download package
  run: |
    cd openwrt && make download -j8
    find dl -size -1024c -exec ls -l {} \;
    find dl -size -1024c -exec rm -f {} \;
```

第五个步骤进入到最重要的开始编译环节，同样是先进入 `buildroot`，为了能更快的编译，我自信的选择了多线程编译且不显示详细日志。

```yaml
- name: Build
  run: |
    cd openwrt
    make -j$(nproc)
```

最后编译出的二进制文件如何取出来呢？官方有个 ac­tion 叫 [upload-artifact](https://p3terx.com/go/aHR0cHM6Ly9naXRodWIuY29tL2FjdGlvbnMvdXBsb2FkLWFydGlmYWN0) ，它可以将虚拟环境中的指定文件打包上传到 Ac­tions 页面。为了方便我选择了上传整个 `bin` 目录，文件名为 `OpenWrt`。

```yaml
- name : Upload artifact
  uses: actions/upload-artifact@master
  with:
    name: OpenWrt
    path: openwrt/bin
```

最后展示一下完整 work­flow 文件：

```yaml
name: Build OpenWrt
on: push
jobs:
  build:
    runs-on: ubuntu-18.04
    steps:
    - name: Installation depends
      run: |
        sudo apt-get update
        sudo apt-get -y install build-essential asciidoc binutils bzip2 gawk gettext git libncurses5-dev libz-dev patch unzip zlib1g-dev lib32gcc1 libc6-dev-i386 subversion flex uglifyjs git-core gcc-multilib p7zip p7zip-full msmtp libssl-dev texinfo libglib2.0-dev xmlto qemu-utils upx libelf-dev autoconf automake libtool autopoint
    - name: Clone source code
      run: |
        git clone https://github.com/openwrt/openwrt
        cd openwrt
        ./scripts/feeds update -a
        ./scripts/feeds install -a
    - name: Generate config file
      run: |
        cd openwrt
        make defconfig
    - name: Download package
      run: |
        cd openwrt && make download -j8
        find dl -size -1024c -exec ls -l {} \;
        find dl -size -1024c -exec rm -f {} \;
    - name: Build
      run: |
        cd openwrt
        make -j$(nproc)
    - name : Upload artifact
      uses: actions/upload-artifact@master
      with:
        name: OpenWrt
        path: openwrt/bin
```

最后 push 到仓库运行。竟然一次成功，话说编译速度是真的快，而且二进制文件也已经上传了，非常完美。



[![img](https://imgcdn.p3terx.com/post/20191113195156.png#vwid=1020&vhei=521)](https://imgcdn.p3terx.com/post/20191113195156.png#vwid=1020&vhei=521)



## 尾巴

自从 GitHub 被微软收购后，从之前的免费私有仓库到现在的 Ac­tions 这样的免费持续集成服务无不彰显出有钱真的可以为所欲为。期待微软把开源世界带向一个新的高度。

------

本博客已开设 [Telegram 频道](https://p3terx.com/go/aHR0cHM6Ly90Lm1lL1AzVEVSWF9aT05F)，欢迎小伙伴们订阅关注。

## 参考资料

[Automating your workflow with GitHub Actions](https://p3terx.com/go/aHR0cHM6Ly9oZWxwLmdpdGh1Yi5jb20vY24vYWN0aW9ucy9hdXRvbWF0aW5nLXlvdXItd29ya2Zsb3ctd2l0aC1naXRodWItYWN0aW9ucw)

[GitHub Actions 入门教程 - 阮一峰的网络日志](https://p3terx.com/go/aHR0cDovL3d3dy5ydWFueWlmZW5nLmNvbS9ibG9nLzIwMTkvMDkvZ2V0dGluZy1zdGFydGVkLXdpdGgtZ2l0aHViLWFjdGlvbnMuaHRtbA)

> **本文作者：**[P3TERX](https://p3terx.com/)
>
> **本文链接：**https://p3terx.com/archives/github-actions-started-tutorial.html
>
> **版权声明：**本博客所有文章除特别声明外，均采用 [CC BY-NC-SA 4.0](https://p3terx.com/go/aHR0cHM6Ly9jcmVhdGl2ZWNvbW1vbnMub3JnL2xpY2Vuc2VzL2J5LW5jLXNhLzQuMC9kZWVkLnpo) 许可协议。非商业转载及引用请注明出处（作者、原文链接），商业转载请联系作者获得授权。

# GitHub Actions 手动触发方式进化史

[P3TERX](https://p3terx.com/author/1/) • 2020-04-02 • [3 评论](javascript:void(0);) • 28283 阅读

## 前言

GitHub Ac­tions 是 Mi­crosoft 收购 GitHub 后推荐的一款 CI/CD 工具。早期可能是处于初级开发阶段，它的功能非常原生，甚至没有直接提供一个手动触发按钮。一般的触发方式为代码变动（`push` 、`pull_request`），发布文件（`release`）或者定时（`schedule`）等，这些属于自动触发方式。如果我们需要在 GitHub 仓库没有任何变动的情况下手动触发就需要使用一些奇技淫巧。经历了漫长的功能迭代，官方最终正式带来了手动触发按钮功能，这也宣告了一个瞎折腾时代的结束，一个崭新的折腾时代开始。

## Star

本博客早前的文章《[使用 GitHub Actions 云编译 OpenWrt](https://p3terx.com/archives/build-openwrt-with-github-actions.html)》中曾介绍过这种触发方式，点击仓库上的 Star 按钮即可触发 GitHub Ac­tions 的工作流程。这是最容易实现的方式，只要 work­flow 文件中存在如下字段：

```none
on:
  watch:
    types: started
```

为了避免被其他人点击 Star 导致的不必要的麻烦，还需要在 work­flow 文件中加上 `if: github.event.repository.owner.id == github.event.sender.id` 字段，这样只有仓库所有者，也就是你自己点 Star 才有效。

```none
jobs:
  build:
    runs-on: ubuntu-latest
    if: github.event.repository.owner.id == github.event.sender.id
```

**点击 Star** 触发方式的 work­flow 文件示例：

```none
name: Test

on:
  watch:
    types: started

jobs:
  build:
    runs-on: ubuntu-latest
    if: github.event.repository.owner.id == github.event.sender.id

    steps:
       - name: Checkout
         uses: actions/checkout@v2
# ...
```

虽然其他人点击不再会触发，但是在 Ac­tions 页面还是会出现一个记录，所以这种手动触发方式并不完美。对于比较大的项目仓库使用可能会因为 Star 太多导致产生很多无意义的 Ac­tions 记录从而影响正常查看 Ac­tions 记录，所以这种方式只适合私有仓库、公开的测试仓库或者不起眼的小项目仓库。

## Webhook

给 GitHub API 发送一个 `repository dispatch event`(仓库调度事件) 请求，当 API 接收到请求后就会触发相应的 work­flow 。Web­hook 方式灵活多变，可控性强，对于高阶用户来说是一个利器，甚至可以自己写一个触发脚本、网页或者浏览器插件来实现更高级的功能。

### 创建 token

首先需要[创建 Personal access token](https://p3terx.com/go/aHR0cHM6Ly9naXRodWIuY29tL3NldHRpbmdzL3Rva2Vucw)，权限为 `repo` 即可。如果你不知道怎么做，可以查看[官方文档中的相关介绍](https://p3terx.com/go/aHR0cHM6Ly9oZWxwLmdpdGh1Yi5jb20vZW4vZ2l0aHViL2F1dGhlbnRpY2F0aW5nLXRvLWdpdGh1Yi9jcmVhdGluZy1hLXBlcnNvbmFsLWFjY2Vzcy10b2tlbi1mb3ItdGhlLWNvbW1hbmQtbGluZQ)。to­ken 会用在 web­hook 的请求标头中，用于身份验证。

### 编写 Workflow 文件

在 work­flow 文件中设置 `repository_dispatch` 触发事件，以便 GitHub Ac­tions 能接收这个触发事件，这是一个最简单的 Work­flow 文件示例：

```none
on: repository_dispatch

jobs:
  run:
    runs-on: ubuntu-latest

    steps:
    - name: Hello World
      run: |
        echo My name is P3TERX.
        echo Hello World!
```

### 发送请求

通过 web­hook 来触发 GitHub Ac­tions，以下是一个使用 cURL 发送请求的例子：

```none
curl -X POST https://api.github.com/repos/:owner/:repo/dispatches \
    -H "Accept: application/vnd.github.everest-preview+json" \
    -H "Authorization: token ACTIONS_TRIGGER_TOKEN" \
    --data '{"event_type": "TRIGGER_KEYWORDS"}'
```

需要要替换的值：

- `:owner` - 用户名
- `:repo` - 需要触发的 Github Action 所在的仓库名称
- `ACTIONS_TRIGGER_TOKEN` - 带有 repo 权限的 Personal access token
- `TRIGGER_KEYWORDS` - 自定义 Webhook 事件名称，可以为任意值，Actions 列表中会显示此名称，更多信息请参见下文。

### 进阶使用

在 web­hook 请求中需要发送一个 `event_type` 属性的 json 有效负载，前面例子中的 `TRIGGER_KEYWORDS` 就是所发送的有效负载，官方将它称之为 “自定义 Web­hook 事件名称”，为了方便理解下文将它称之为 “触发关键词”。因为没有做任何限制设置，所以可以为任意值，这是最基础的使用方式。有时一个仓库可能不止一个 work­flow ，所以我们就可能需要对触发关键词进行限制，使用不同的关键词来触发不同的 work­flow 。

设置 `repository_dispatch` 下的 `types` 字段的值可以限制触发关键词，下面的例子将设置 `helloworld` 这个触发关键词，只有当请求中的关键词为 `helloworld` 才会触发。

```none
on:
  repository_dispatch:
    types: helloworld
```

还可以给每一个步骤设置运行条件，在运行条件中 `github.event.action` 等于触发关键词，通过判断给定的值是否与 `github.event.action` 相同来判断该步骤是否需要执行。比如下面例子中只有当触发关键词为 `helloworld` 时才会执行这个步骤。

```none
steps:
- name: Hello World
  if: github.event.action == 'helloworld'
  run: |
    echo My name is P3TERX.
    echo Hello World!
```

通过判断 `github.event.action` 是否包含给定的值来判断该步骤是否需要执行。比如下面例子中当关键词包含 `hello` 这个步骤就会执行。

```none
steps:
- name: Hello World
  if: contains(github.event.action, 'hello')
  run: |
    echo My name is P3TERX.
    echo Hello World!
```

触发关键词也可以是多个，比如像下面这个例子。当触发关键词为 `helloworld` 时，只有 `Hello World` 步骤会运行；当触发关键词为 `test` 时，只有 `TEST` 步骤会运行；当触发关键词为 `none` 时，虽然 Ac­tions 会触发，但没有步骤运行。而发送其他关键词并不会触发这个 work­flow 。

```none
name: Webhook Test

on:
  repository_dispatch:
    types: [helloworld, test, none]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Hello World
      if: contains(github.event.action, 'hello')
      run: |
        echo My name is P3TERX.
        echo Hello World!

    - name: TEST
      if: github.event.action == 'test'
      run: |
        echo test
```

甚至还可以在一个 work­flow 完成后调用另一个 work­flow ，只需要在 work­flow 结尾加入相应的请求指令即可。具体就不做赘述了，相信看到这里的小伙伴都应该已经了解该如何去做了。

## 手动触发按钮

在时隔多年后 GitHub Ac­tions 终于引入了一个手动触发的按钮，不过默认是不开启的，需要在 work­flow 文件中设置 `workflow_dispatch` 触发事件。一个最简单的例子：

```none
on:
  workflow_dispatch:
```

设置好触发事件后就能在相关 work­flow 的页面下看到 `Run workflow` 按钮.



更复杂一点还可以实现在手动触发时填写参数，控制不同的工作流程或者直接改写某个环境变量等操作。目前官方文档已经相当完善，所以博主就不赘述了，感兴趣的小伙伴可以去参考[官方文档中的相关章节](https://p3terx.com/go/aHR0cHM6Ly9kb2NzLmdpdGh1Yi5jb20vY24vYWN0aW9ucy9jb25maWd1cmluZy1hbmQtbWFuYWdpbmctd29ya2Zsb3dzL2NvbmZpZ3VyaW5nLWEtd29ya2Zsb3cjbWFudWFsbHktcnVubmluZy1hLXdvcmtmbG93)。

## 尾巴

从最初的只能选择 Star ，到后来深入了解研究了 web­hook 触发方式自己写脚本、网页去触发，GitHub Ac­tions 促使我学习了很多新的东西。如果当初 GitHub 直接给了一个触发按钮，也许就不会去折腾了，也少了很多乐趣。这是 GitHub Ac­tions 的进化史，也是我自己的进化史。

------

本博客已开设 [Telegram 频道](https://p3terx.com/go/aHR0cHM6Ly90Lm1lL1AzVEVSWF9aT05F)，欢迎小伙伴们订阅关注。

## 参考资料

[Setting up Webhooks for Github Actions](https://p3terx.com/go/aHR0cDovL3d3dy5idGVsbGV6LmNvbS9wb3N0cy90cmlnZ2VyaW5nLWdpdGh1Yi1hY3Rpb25zLXdpdGgtd2ViaG9va3MuaHRtbA)

[GitHub Actions Docs: External events: repository_dispatch](https://p3terx.com/go/aHR0cHM6Ly9oZWxwLmdpdGh1Yi5jb20vZW4vYWN0aW9ucy9yZWZlcmVuY2UvZXZlbnRzLXRoYXQtdHJpZ2dlci13b3JrZmxvd3MjZXh0ZXJuYWwtZXZlbnRzLXJlcG9zaXRvcnlfZGlzcGF0Y2g)

[GitHub API Docs: Create a repository dispatch event](https://p3terx.com/go/aHR0cHM6Ly9kZXZlbG9wZXIuZ2l0aHViLmNvbS92My9yZXBvcy8jY3JlYXRlLWEtcmVwb3NpdG9yeS1kaXNwYXRjaC1ldmVudA)

[GitHub Actions: Manual triggers with workflow_dispatch](https://p3terx.com/go/aHR0cHM6Ly9naXRodWIuYmxvZy9jaGFuZ2Vsb2cvMjAyMC0wNy0wNi1naXRodWItYWN0aW9ucy1tYW51YWwtdHJpZ2dlcnMtd2l0aC13b3JrZmxvd19kaXNwYXRjaC8)

> **本文作者：**[P3TERX](https://p3terx.com/)
>
> **本文链接：**https://p3terx.com/archives/github-actions-manual-trigger.html
>
> **版权声明：**本博客所有文章除特别声明外，均采用 [CC BY-NC-SA 4.0](https://p3terx.com/go/aHR0cHM6Ly9jcmVhdGl2ZWNvbW1vbnMub3JnL2xpY2Vuc2VzL2J5LW5jLXNhLzQuMC9kZWVkLnpo) 许可协议。非商业转载及引用请注明出处（作者、原文链接），商业转载请联系作者获得授权。
# Actions-Note

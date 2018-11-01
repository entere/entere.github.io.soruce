---
title: 用PHPcs规范你的PHP开发
date: 2018-11-01 22:36:18
tags: [PHPcs]
---
# MAC下安装PHPcs

这里我们使用comooser 安装PHPcs

```
composer global require squizlabs/php_codesniffer
composer global require phpmd/phpmd
composer global require friendsofphp/php-cs-fixer

```
安装好后会在 ~/.composer/vendor/bin 下看到

```
pdepend      php-cs-fixer phpcbf       PHPcs        phpmd

```

# 配置环境变量

vi ~/.bash_prifole 在结尾处添加

```
export PATH="~/.composer/vendor/bin:$PATH"

```

然后执行 source ~/.bash_prifole

这里 PHPcs --help命令就生效了

# 在Sublime 下使用 PHPcs

command+Shift+P 输入 install 找到PHPcs并安装

安装好后在这Preferences -> Package Setting -> PHP Code Sniffer -> Setting – User中添加添加配置文件

```
{

    "phpcs_executable_path": "~/.composer/vendor/bin/phpcs",
    "php_cs_fixer_executable_path": "~/.composer/vendor/bin/php-cs-fixer",
    "phpcbf_executable_path": "~/.composer/vendor/bin/phpcbf",
    "phpmd_executable_path": "~/.composer/vendor/bin/phpmd",
    "phpcs_additional_args": {
        "--standard": "PSR2",
        "-n": ""
    },
    "phpcs_show_errors_on_save": true,
    "phpcs_outline_for_errors": false,
    "php_cs_fixer_on_save": false

}
```

好了，不符合规范的代码的save里就会提示出来
# 简介
本项目来自网友的灵感。  
项目的初衷是将题解与OI博客聚集在一起，让OIer们快速方便地学习与提高。  
积少成多，本项目需要大家共同维护，欢迎各路大佬提交PR。  

- [编写题解](#编写您的题解)  
- [阅读题解](#阅读题解)  

# 编写您的题解

## 格式要求
对于每一道题在根目录下新建文件夹，文件夹名为题目编号，如`/1000`。  
默认题解文件名为`solution-name.md`，其中`name`是您的昵称（想写其他的也成），**必须使用markdown格式编写题解**，若您不了解markdown格式，请参考下面的[帮助](#markdown帮助)。  
若您的题解需要用到图片，请将图片放在相对位置的`/img`文件夹中，如`/1000/img/1.jpg`。  
如果需要用到其它附加文件，请将它们放在相对位置的`/file`文件夹下。  

这是架构图：  
```yml
├── 1000                 #1000题
├── 1001                 #1001题
|   ├── img              #图片
|   |   ├── 1.jpg
|   |   └── 2.svg
|   ├── file             #附加文件
|   |   ├── test.cpp
|   |   └── check.bat
|   ├── solution-Jack.md #Jack的题解文件
|   └── solution-Bob.md  #Bob的题解文件
```

**若您的题解不符合以上要求，我们将拒绝合并您的Pull request**  

## 范例
为了方便您理解题解格式，我们在这里放入了一份范例供您查阅。  
[范例：solution-billyang](/1131)

## 我们的请求
- 希望您的题解能够做到详细清晰，不要变成一句话题解。  
- 希望您可以在题解后配上代码。  

## 可选选项
- 您可以在题解头部插入题目描述、输入说明等...
- 您可以在文章尾部署名，打广告等...
- 在您的PR中，可以在[底部贡献者清单](贡献者（排名不分先后）)添上您的联系方式。  

## Markdown帮助
[Markdown 简明语法手册](https://www.zybuluo.com/mdeditor?url=https://www.zybuluo.com/static/editor/md-help.markdown)  
[Markdown 公式指导手册](https://www.zybuluo.com/codeep/note/163962)  

# 阅读题解
推荐使用[Moeditor](https://moeditor.github.io/)阅读题解。  
您也可以使用[Cmd Markdown](https://www.zybuluo.com/mdeditor?url=https://www.zybuluo.com/static/editor/md-help.markdown)阅读题解。  

操作的步骤是：  
1. 下载题解文件（若有图片文件请将`/img`文件夹一同下载）  
2. 使用Moeditor或其他Markdown阅读器打开题解文件 / 将题解文件内容复制到Cmd Markdown编辑器中。  
3. 在右侧查看题解。  

# 贡献者（排名不分先后）
- [Bill Yang](https://bill.moe)
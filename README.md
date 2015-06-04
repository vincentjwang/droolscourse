# Drools介绍和环境搭建

关于Drools的介绍，我们看下官网的描述：
>Drools is a Business Rules Management System (BRMS) solution. It provides a core Business Rules Engine (BRE), a web authoring and rules management application (Drools Workbench) and an Eclipse IDE plugin for core development.

Drools是一个业务规则管理系统解决方案。它提供一整套核心业务规则引擎(BRE)，Web验证规则管理应用(Drools Workbench)和Eclipse IDE插件。我们用到的只是解决方案中的规则引擎部分，它允许开发人员将业务规则以规则文件的形式存储，这样业务改变只需要修改规则文件，不需要修改代码部署版本即可完成业务的引入或变更。

## Drools安装
Drools的开发环境搭建通常有两种方式，本人亲测两种都能安装成功，不过个人更倾向于第一种，也是官方推荐的方法。
### 基础环境
OS: Ubuntu10.10
JRE version: 1.8.0_45
IDE: Eclipse
### Installation Method 1 - Eclipse install new software
到Drools下载页面(http://www.jboss.org/drools/downloads.html)
-下载并解压Drools：drools-distribution-5.5.0.Final.zip  (解压目录随意)
-下载并解压插件：droolsjbpm-tools-distribution-5.5.0.Final.zip(解压目录随意)
- 打开 Eclipse.
- 点 "Help"—"Install new software..."
- 点击 "Add..."  
- 在name中输入"Drools5.5"(名字随意起)
- 点击 "Local..." 并且选择"......./binaries/org.drools.updatesite" (此目录在刚刚解压的插件中)
- 点击OK
- 选择所以插件。
-下面就是点击next，finish
 
插件安装完后还需在Eclipse中添加Drools的运行环境：
- 点击"Windows"—"Preferencess"
- 在窗口的树形菜单中选择"Drools"—"Installed Drools Runtimes"
- 在右边的空的Drools运行库列表中，点击"Add"
- 在弹出窗口中name输入Drools5.5 Runtime(随意起个名字)，在Path选择"......./drools-distribution-5.5.0.Final/drools-distribution-5.5.0.Final/binaries" (此目录在刚刚解压的Drools中)，
- 点击"OK"
- 点击"OK"

### Installation Method 2 - Plugins link
下载Method1中的两个压缩包
* 在Eclipse安装目录下创建两个文件links, myPlugins(名字随意)
* 在myplugin下新建一个drools文件夹。。将以解压后的文件的上一层目录下features,plugins 复制到里面
* 在 links 目录下创建一个 drools.txt(名字随意)，打开并输入：path=(Eclipse路径)\\eclipse\\myplugins\\drools
* 保存关闭 drools.txt，并将后缀改成 drools.link，重启 eclipse
* 安装运行时方式如Method 1

## 示例程序

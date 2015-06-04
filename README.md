# Drools介绍和环境搭建

关于Drools的介绍，我们看下官网的描述：
>Drools is a Business Rules Management System (BRMS) solution. It provides a core Business Rules Engine (BRE), a web authoring and rules management application (Drools Workbench) and an Eclipse IDE plugin for core development.

Drools是一个业务规则管理系统解决方案。它提供一整套核心业务规则引擎(BRE)，Web验证规则管理应用(Drools Workbench)和Eclipse IDE插件。我们用到的只是解决方案中的规则引擎部分，它允许开发人员将业务规则以规则文件的形式存储，这样业务改变只需要修改规则文件，不需要修改代码部署版本即可完成业务的引入或变更。

## Drools安装
Drools的开发环境搭建通常有两种方式，本人亲测两种都能安装成功，不过个人更倾向于第一种，也是官方推荐的方法。
### 基础环境
* OS: Ubuntu10.10
* JRE version: 1.8.0_45
* IDE: Eclipse
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
打开Eclipse -> File -> New -> Drools Project，输入项目名后会有示例程序的选项，先全部勾选上吧。
可以看到生成的项目里包含了很多文件，我们暂时先不用理清每个文件的含义，重点关注src/main/java/com.sample/DroolsTest.java和src/main/rules/Sample.drl

DroolsTest.java
```java
package com.sample;

import org.drools.KnowledgeBase;
import org.drools.KnowledgeBaseFactory;
import org.drools.builder.KnowledgeBuilder;
import org.drools.builder.KnowledgeBuilderError;
import org.drools.builder.KnowledgeBuilderErrors;
import org.drools.builder.KnowledgeBuilderFactory;
import org.drools.builder.ResourceType;
import org.drools.io.ResourceFactory;
import org.drools.logger.KnowledgeRuntimeLogger;
import org.drools.logger.KnowledgeRuntimeLoggerFactory;
import org.drools.runtime.StatefulKnowledgeSession;

/**
 * This is a sample class to launch a rule.
 */
public class DroolsTest {

    public static final void main(String[] args) {
        try {
            // load up the knowledge base
            KnowledgeBase kbase = readKnowledgeBase();
            StatefulKnowledgeSession ksession = kbase.newStatefulKnowledgeSession();
            KnowledgeRuntimeLogger logger = KnowledgeRuntimeLoggerFactory.newFileLogger(ksession, "test");
            // go !
            Message message = new Message();
            message.setMessage("Hello World");
            message.setStatus(Message.HELLO);
            ksession.insert(message);
            ksession.fireAllRules();
            logger.close();
        } catch (Throwable t) {
            t.printStackTrace();
        }
    }

    private static KnowledgeBase readKnowledgeBase() throws Exception {
        KnowledgeBuilder kbuilder = KnowledgeBuilderFactory.newKnowledgeBuilder();
        kbuilder.add(ResourceFactory.newClassPathResource("Sample.drl"), ResourceType.DRL);
        KnowledgeBuilderErrors errors = kbuilder.getErrors();
        if (errors.size() > 0) {
            for (KnowledgeBuilderError error: errors) {
                System.err.println(error);
            }
            throw new IllegalArgumentException("Could not parse knowledge.");
        }
        KnowledgeBase kbase = KnowledgeBaseFactory.newKnowledgeBase();
        kbase.addKnowledgePackages(kbuilder.getKnowledgePackages());
        return kbase;
    }

    public static class Message {

        public static final int HELLO = 0;
        public static final int GOODBYE = 1;

        private String message;

        private int status;

        public String getMessage() {
            return this.message;
        }

        public void setMessage(String message) {
            this.message = message;
        }

        public int getStatus() {
            return this.status;
        }

        public void setStatus(int status) {
            this.status = status;
        }

    }

}
```
Sample.drl
```java
package com.sample
 
import com.sample.DroolsTest.Message;
 
rule "Hello World"
    when
        $m : Message( status == Message.HELLO, $myMessage : message )
    then
        System.out.println( $myMessage );
        $m.setMessage( "Goodbye cruel worlds" );
        $m.setStatus( Message.GOODBYE );
        update( $m );
end

rule "GoodBye"
    when
        Message( status == Message.GOODBYE, $myMessage : message )
    then
        System.out.println( $myMessage );
end
```

运行下试试看，可以看到控制台输出了
Hello World   </br>
Goodbye cruel worlds   </br>

可以看到我们并没有在java代码里写业务逻辑代码，业务都是包含在规则中的，这就是规则引擎的功劳了，以后的章节我们会详细分析。

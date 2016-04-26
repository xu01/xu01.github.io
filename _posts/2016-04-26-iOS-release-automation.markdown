---
layout: post
title:  "iOS项目自动打包(一)"
date:   2016-04-26 09:12:00 +0800
categories: iOS
---

### 开始 ###
<p>&emsp;&emsp;一切的一切都源自于“懒”，iOS开发过程中固定时间版本Release发布，自己手动Xcode Archive一下也不觉得什么，但临时的QA来要求测试，会令我这种“易烦躁”的开发者觉得麻烦，甚至也可能打断我当前的手头工作，因为极有可能你代码撸了一半未测试及未提交，却需要打包一个版本库中小修小改的版本。所以只能遍寻方法来解决这种问题，解脱自己。</p>
<p>&emsp;&emsp;其实无论哪种你希望开发不受打扰的打包方式，前提都是你需要另一台专门的 Mac 打包设备。</p>
<p>&emsp;&emsp;大致亲测了三种打包方式: Jenkins + Xcode plugin, Mac OSX Server + Xcode Bot, Shell命令脚本，这里大致记录下其中的一些过程和坑。</p>

## Jenkins + Xcode plugin ##
<p>&emsp;&emsp;安装 Jenkins , 两种方式: 官网下载pkg 或 brew, 这里我直接用 brew 安装的</p>
<p>&emsp;&emsp;安装完成之后，直接终端里 jenkins 就能启动，默认端口 8080，你可以在 safari 中通过 http://localhost:8080/ 来进行访问</p>
<p>&emsp;&emsp;如果需要停止 Jenkins 服务的话，可以直接通过访问 http://localhost:8080/exit 来完成</p>
<p>&emsp;&emsp;接下来说到 Jenkins 的配置: 需要在 Manage Jenkins 中的 Manage Plugins 查找并安装 Git plugin, Keychains and Provisioning Profiles Management, Xcode integration</p>

<p>&emsp;&emsp;之后还是在 Manage Jenkins 下 会有Keychains and Provisioning Profiles Management 这是一个证书管理工具，你需要将一些打包所需要的证书信息添加到这里</p>
<p>![Keychains and Provisioning Profiles Management](/assets/images/2016-04-26/iOS_release_automation_keychains_plugin.jpg)</p>




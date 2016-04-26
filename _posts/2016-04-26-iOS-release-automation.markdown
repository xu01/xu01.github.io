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

<p>&emsp;&emsp;之后还是在 Manage Jenkins 下 会有Keychains and Provisioning Profiles Management 这是一个证书管理工具，你需要将一些打包所需要的证书信息(包括你钥匙圈中证书所属的keychain以及provision)添加到这里</p>
![Keychains and Provisioning Profiles Management 1](/assets/images/2016-04-26/iOS_release_automation_1_keychains_plugin_1.png)
![Keychains and Provisioning Profiles Management 2](/assets/images/2016-04-26/iOS_release_automation_1_keychains_plugin_2.png)

<p>&emsp;&emsp;随后开始创建项目，之前添加的 Git plugin 信息，如图:</p>
![Git Plugin configure](/assets/images/2016-04-26/iOS_release_automation_1_git_plugin_1.png)
<p>&emsp;&emsp;Build Environment 中的 Keychains and Code Signing Identifies 对应选择之前在 Keychains and Provisioning Profiles Management 中 添加的对应信息，如图:
![Keychains and Code Signing Identifies](/assets/images/2016-04-26/iOS_release_automation_1_keychain_and_code_signing_identifies.png)
<p>&emsp;&emsp;Mobile Provision Profile 中对应选择之前在 Keychains and Provisioning Profiles Management 中 添加的对应信息，如图:
![Mobile Provision Profile](/assets/images/2016-04-26/iOS_release_automation_1_mobile_provision_profile.png)
<p>&emsp;&emsp;Xcode 部分按照自己的需求勾选填写自己的项目信息，值得注意的是，项目中有Cocoapods，一定要填写 workspace 的 xcworkspace 文件(我的项目Git中是带有Pods文件夹的，如果只带有Podfile文件，而忽略Pods文件夹的话，还需要 Add build step 中添加 Execute shell来执行对应的pod install 或 update 等操作)，如图:</p>
![Xcode plugin](/assets/images/2016-04-26/iOS_release_automation_1_xcode_plugin_1.png)
![Xcode plugin](/assets/images/2016-04-26/iOS_release_automation_1_xcode_plugin_2.png)
![Xcode plugin](/assets/images/2016-04-26/iOS_release_automation_1_xcode_plugin_3.png)

<p>至此，Jenkins的配置部分基本就结束了，这里值得注意的有几点：</p>
* 我的项目Git中是带有Pods文件夹的，如果只带有Podfile文件，而忽略Pods文件夹的话，还需要 Add build step 中添加 Execute shell来执行对应的pod install 或 update 等操作
* login.keychain 所在目录是 ~/Library/Keychains/ 并且检查你所使用的 Certificate 属于该 Keychain，当然你也可以新建一个 keychain 将证书添加进去

<p>接下来讲两个Xcode中会遇到的问题：</p>
* 因为之前都是利用Xcode手动打包的，所以几乎Schemes中的Shared都未选中，但苹果的文档中似乎提到了在使用Xcode Server之类的automatic integration时需要勾选Shared([Sharing Schemes]: https://developer.apple.com/library/ios/recipes/xcode_help-scheme_editor/Articles/SchemeShare.html)
![Sharing Schemes](/assets/images/2016-04-26/iOS_release_automation_1_sharing_schemes.png)
* 另外就是需要在Code Signing Resource Rules Path中填写对应的 SDK Resource Rule 的 plist 文件路径 ([Code Signing Resource Rules Path]: http://stackoverflow.com/questions/26516442/how-do-we-manually-fix-resourcerules-plist-cannot-read-resources-error-after)
![Code Signing Resource Rules Path](/assets/images/2016-04-26/iOS_release_automation_1_code_signing_resource_rules_path.png)

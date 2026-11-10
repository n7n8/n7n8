### 打开“Google设置”（小米、荣耀国行手机可能有麻烦）

<!--

[小米打开谷歌菜单](https://web.vip.miui.com/page/info/mio/mio/detail?app_version=dev.20051&postId=37895838)

[【DP】小米 17 Ultra 国行 HyperOS 4 如何关闭 Google 自动验证电话号码（设置里找不到Google的解决办法）](https://www.uscardforum.com/t/topic/542874)

-->

adb命令行

```
adb shell am start -n com.google.android.gms/com.google.android.gms.app.settings.GoogleSettingsIALink
```

Package Name：com.google.android.gms

Activity：com.google.android.gms.app.settings.GoogleSettingsIALink

Activity启动器推荐：
<https://github.com/sdex/ActivityManager>

### CN-GMS 标签手机（Google Play无法自更新）更新 Google Play 入口

[APKMirror](https://www.apkmirror.com/apk/google-inc/google-play-store/)

[APKPure](https://apkpure.com/google-play-store-app/com.android.vending/amp)

### HyperOS 通行密钥坑

<!--
[通行密钥在 HyperOS 2 无法使用（已更新OS3）](https://web.vip.miui.com/page/info/mio/mio/detail?postId=47465346)

[小米澎湃OS4修改三方通行密钥App解决方案](https://www.lzskyline.com/archives/xiao-mi-peng-pai-os4xiu-gai-san-fang-tong-xing-mi-yao-appjie-jue-fang-an)

[修复小米 / HyperOS 无法弹出 Passkey 的问题](https://milu.ink/627.html)
-->

扫描此设备所有支持凭据服务的app及其凭据服务名：

```
adb shell cmd package query-services --brief -a android.service.credentials.CredentialProviderService
```

查看当前凭据服务（Passkey相关）启用列表及首选：

注：credential_service为已开启服务列表；credential_service_primary为首选服务。Android 14+支持多个凭据共存，如果启用了多个，它们会以冒号 : 分隔。

```
adb shell settings get secure credential_service
```
```
adb shell settings get secure credential_service_primary
```

更改当前凭据服务（Passkey相关）启用列表及首选：

```
adb shell settings put secure credential_service com.x8bit.bitwarden/com.x8bit.bitwarden.Autofill.CredentialProviderService
```
```
adb shell settings put secure credential_service_primary com.x8bit.bitwarden/com.x8bit.bitwarden.Autofill.CredentialProviderService
```

以下是一些已知的凭据服务名

- Google密码管理器：com.google.android.gms/com.google.android.gms.auth.api.credentials.credman.service.PasswordAndPasskeyService
- Bitwarden：com.x8bit.bitwarden/com.x8bit.bitwarden.Autofill.CredentialProviderService
- Microsoft Authenticator：com.azure.authenticator/com.microsoft.authenticator.passkeys.businessLogic.AuthenticatorCredentialService
- Microsoft Edge：com.microsoft.emmx/org.chromium.chrome.browser.webauthn.edge.PasskeyCredentialProviderService

最后，如果有root，一个命令行解决
```
adb shell pm uninstall --user 0 com.miui.contentcatcher
```

### Android 手机 Material You 动态色彩设置（包括小米HyperOS）

以紫色`#746BC1`为例：

```
adb shell settings put secure theme_customization_overlay_packages '{"android.theme.customization.system_palette":"746BC1"}'
```

### 逐个 app 设置语言

1. 直接打开隐藏的“应用语言”设置页

如果你只是想看看系统里是否藏着这个界面，可以用命令强行把它拉出来：

```
adb shell am start -a android.settings.APP_LOCALE_SETTINGS -d "package:com.absinthe.libchecker"
```

把应用包名替换成你想改的 App（比如 com.absinthe.libchecker ）。如果系统支持，会直接跳到那个 App 的语言选择页。

### 三星浏览器改区

1. 打开
```
internet://debug
```
2. 打开“设置→Debug settings→Feature variation test”
3. 将Country code(China)、Country iso code(CN)改掉

---

### 微信 XWeb 微操

动态加载配置：

<https://dldir1v6.qq.com/weixin/android/wxweb/updateConfig_dev.xml> 开发版

<https://dldir1v6.qq.com/weixin/android/wxweb/updateConfig.xml> 正式版

微信使用Android System Webview：
<http://debugxweb.qq.com/?reset_webview_kind_sys>

微信使用XWeb内核：
<http://debugxweb.qq.com/?reset_webview_kind_xweb>

微信更新体验版XWeb：
<http://debugxweb.qq.com/?set_config_url=https://dldir1v6.qq.com/weixin/android/wxweb/updateConfig_dev.xml&check_xwalk_update>

微信退回正式版XWeb：
<http://debugxweb.qq.com/?set_config_url=https://dldir1v6.qq.com/weixin/android/wxweb/updateConfig.xml&check_xwalk_update>

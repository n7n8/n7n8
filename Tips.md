### 打开“Google设置”（小米、荣耀国行手机可能有麻烦）

[小米打开谷歌菜单](https://web.vip.miui.com/page/info/mio/mio/detail?app_version=dev.20051&postId=37895838)

[【DP】小米 17 Ultra 国行 HyperOS 4 如何关闭 Google 自动验证电话号码（设置里找不到Google的解决办法）](https://www.uscardforum.com/t/topic/542874)

adb命令行

```
adb shell am start -n com.google.android.gms/com.google.android.gms.app.settings.GoogleSettingsIALink
```

Package Name：com.google.android.gms

Activity：com.google.android.gms.app.settings.GoogleSettingsIALink

Activity启动器推荐：
<https://github.com/sdex/ActivityManager>

### CN-GMS 手机更新 Google Play 入口

[APKMirror](https://www.apkmirror.com/apk/google-inc/google-play-store/)

### HyperOS 通行密钥坑

[通行密钥在 HyperOS 2 无法使用（已更新OS3）](https://web.vip.miui.com/page/info/mio/mio/detail?postId=47465346)

[小米澎湃OS4修改三方通行密钥App解决方案](https://www.lzskyline.com/archives/xiao-mi-peng-pai-os4xiu-gai-san-fang-tong-xing-mi-yao-appjie-jue-fang-an)

根据包名搜索密码管理器app的凭据服务名：

```
adb shell dumpsys package com.kaspersky.passwordmanager | grep "CredentialProvider"
```

查看当前凭据服务（Passkey相关）启用列表及首选：

注：credential_service为已开启服务列表；credential_service_primary为首选服务。Android 14+支持多个凭据共存，如果启用了多个，它们会以冒号 : 分隔。

- Google密码管理器：com.google.android.gms/com.google.android.gms.auth.api.credentials.credman.service.PasswordAndPasskeyService
- Bitwarden：com.x8bit.bitwarden/com.x8bit.bitwarden.Autofill.CredentialProviderService
- Microsoft Authenticator：com.azure.authenticator/com.microsoft.authenticator.passkeys.businessLogic.AuthenticatorCredentialService
- Microsoft Edge：com.microsoft.emmx/org.chromium.chrome.browser.webauthn.edge.PasskeyCredentialProviderService

```
adb shell settings get secure credential_service
adb shell settings get secure credential_service_primary
```

更改当前凭据服务（Passkey相关）启用列表及首选：

```
adb shell settings put secure credential_service com.x8bit.bitwarden/com.x8bit.bitwarden.Autofill.CredentialProviderService
adb shell settings put secure credential_service_primary com.x8bit.bitwarden/com.x8bit.bitwarden.Autofill.CredentialProviderService
```

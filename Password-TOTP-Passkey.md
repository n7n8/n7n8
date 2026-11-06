## 中国大陆 Android 的常态（对 GitHub 尤为明显）

很多用户从没用过正经密码管理器（Bitwarden、1Password、KeePass 等），账号密码全靠脑子记或手机备忘录。

Passkey（通行密钥）几乎没人主动推广，知道的人不多。

恢复码（recovery codes）往往随手截图或直接丢掉，出事时才发现救不回来。

TOTP 找不到好用的（找到能云同步的更难），一换手机就丢。

2FA（两步验证）配置全靠自己摸索，很多人只会用短信，或者根本懒得开。

## 为什么

1. 海外 Android 的密码管理器、凭据管理器，默认是 Google Password Manager。
2. 海外 Android 的 TOTP 管理器默认是 Google Authenticator。
3. 中国大陆 Android 仅有的能跨品牌手机、实现手机 <-> PC同步的密码自动填充app是 Edge。
4. 中国大陆 Android 仅有的能低成本用的 TOTP 管理器是 Microsoft Authenticator。但没有云同步，且不支持以通用明文格式直接导入或导出双重认证（2FA/TOTP）账户，意味着换机即丢。Aegis Authenticator 成本过高（Aegis 官方分发渠道：Google Play、F-Droid、GitHub Release）。
5. 中国大陆 Android 缺少能跨品牌手机、实现手机 <-> PC同步的 Passkey 管理器。

## 在 Google Play 环境下该怎么用 Google 的「身份验证与恢复凭据」服务

[Google Authenticator](https://play.google.com/store/apps/details?id=com.google.android.apps.authenticator2)

[Google 密码管理器（快捷方式）](https://play.google.com/store/apps/details?id=com.google.android.apps.credentialmanager)

### Android 通过 adb 设置 Autofill Service（涉及密码填充）

扫描此设备所有支持Autofill Service的app及其完整组件名：

```
adb shell cmd package query-services --brief -a android.service.autofill.AutofillService
```

查看当前Autofill Service状态：

```
adb shell settings get secure autofill_service
```

启用 Google 自动填充：

```
adb shell settings put secure autofill_service com.google.android.gms/.autofill.service.AutofillService
```

### Android 通过 adb 设置 Credential Service (涉及Passkey)（小米HyperOS通用）

扫描此设备所有支持Credential Service的app及其完整组件名：

```
adb shell cmd package query-services --brief -a android.service.credentials.CredentialProviderService
```

查看当前Credential Service启用列表及首选：

注：credential_service为已开启服务列表；credential_service_primary为首选服务。Android 14+支持多个凭据共存，如果启用了多个，它们会以冒号 : 分隔。

```
adb shell settings get secure credential_service
```
```
adb shell settings get secure credential_service_primary
```

更改当前Credential Service启用列表及首选（以Google密码管理器为例）：

请在“settings put secure credential_service com.google.android.gms/com.google.android.gms.auth.api.credentials.credman.service.PasswordAndPasskeyService”之后添加所有你需要的凭据服务（多个凭据服务名以冒号 : 分隔）

```
adb shell settings put secure credential_service com.google.android.gms/com.google.android.gms.auth.api.credentials.credman.service.PasswordAndPasskeyService
```
```
adb shell settings put secure credential_service_primary com.google.android.gms/com.google.android.gms.auth.api.credentials.credman.service.PasswordAndPasskeyService
```

- Google 密码管理器的 Credential Service 的完整组件名：
com.google.android.gms/com.google.android.gms.auth.api.credentials.credman.service.PasswordAndPasskeyService

附个[FIDO Bridge](https://play.google.com/store/apps/details?id=com.token2.companion)，可能会修复硬件通行密钥问题。

特别感谢↓

[通行密钥在 HyperOS 2 无法使用（已更新OS3）](https://web.vip.miui.com/page/info/mio/mio/detail?postId=47465346)

[小米澎湃OS4修改三方通行密钥App解决方案](https://www.lzskyline.com/archives/xiao-mi-peng-pai-os4xiu-gai-san-fang-tong-xing-mi-yao-appjie-jue-fang-an)

[修复小米 / HyperOS 无法弹出 Passkey 的问题](https://milu.ink/627.html)

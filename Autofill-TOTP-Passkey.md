## 中国大陆 Android 的常态（对 GitHub 尤为明显）

很多用户从没用过正经密码管理器（Bitwarden、1Password、KeePass 等），账号密码全靠脑子记或手机备忘录。

Passkey（通行密钥）几乎没人主动推广，知道的人不多。

恢复码（recovery codes）往往随手截图或直接丢掉，出事时才发现救不回来。

TOTP 找不到好用的（找到能云同步或能本地导出的更难），一换手机就丢。

2FA（两步验证）配置全靠自己摸索，很多人只会用短信，或者根本懒得开。

## 为什么

> 真正需要跨平台协作的高阶用户（如 GitHub 开发者、出海从业者、极客），发现中国大陆 Android 基本没有一个能打的 TOTP/Passkey “本土替代品”。

1. 海外 Android 的密码管理器、凭据管理器，默认是 Google Password Manager。
2. 海外 Android 的 TOTP 管理器多数是 Google Authenticator。
3. 中国大陆 Android 仅有的获取门槛低、能跨品牌手机、实现手机 <-> PC同步的密码自动填充 app 是 Edge。
4. 中国大陆 Android 仅有的能低操作门槛使用的 TOTP 管理器 App 是 Microsoft Authenticator。但[在 2027 年后云同步为 Google One](https://support.microsoft.com/en-us/authenticator/back-up-your-accounts-in-microsoft-authenticator)，且不支持以通用明文格式直接导入或导出双重认证（2FA/TOTP）账户，意味着很大概率换机即丢。Aegis Authenticator 成本过高（Aegis 官方分发渠道：Google Play、F-Droid、GitHub Release）。
5. 中国大陆 Android 能跨品牌手机、实现手机 <-> PC同步的 Passkey 管理器也只有 Edge（Microsoft 密码管理器）。

## 如何配置的「身份验证与恢复凭据」服务（推荐 Google Play 环境）

### TOTP 软件选择

Aegis Authenticator（[Google Play](https://play.google.com/store/apps/details?id=com.beemdevelopment.aegis) [GitHub](https://github.com/beemdevelopment/Aegis/releases) [F-Droid](https://f-droid.org/packages/com.beemdevelopment.aegis/)）<br>
如果无法直接访问 F-Droid：[清华源](https://mirrors.tuna.tsinghua.edu.cn/fdroid/repo/?fingerprint=43238D512C1E5EB2D6569F4A3AFBF5523418B82E0A3ED1552770ABB9A9C9CCAB) [CERNET 联合镜像](https://mirrors.cernet.edu.cn/fdroid/repo/?fingerprint=43238D512C1E5EB2D6569F4A3AFBF5523418B82E0A3ED1552770ABB9A9C9CCAB)

Google Authenticator（[Google Play](https://play.google.com/store/apps/details?id=com.google.android.apps.authenticator2)）

微信小程序“数盾OTP”算是勉强可行选择<br>（清除小程序数据后仍能自动同步，但无法导出核心密钥）

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

以下是一些已知的自动填充服务名：

- Google Play services（Google 密码管理器）：`com.google.android.gms/.autofill.service.AutofillService`
- Android Credential Manager：`com.android.credentialmanager/.autofill.CredentialAutofillService`
- Bitwarden：`com.x8bit.bitwarden/.Autofill.CredentialProviderService`
- Microsoft Edge（Microsoft 密码管理器）：`com.microsoft.emmx/org.chromium.chrome.browser.edge_autofill.EdgeAutofillService`
- Firefox：`org.mozilla.firefox/org.mozilla.fenix.autofill.AutofillService`
- Brave：`com.brave.browser/org.chromium.chrome.browser.autofill.BraveAutofillService`
- Opera：`com.opera.browser/com.opera.android.autofill.OperaAutofillService`
- Yandex：`com.yandex.browser/.autofill.service.ExternalAutofillService`

### Android 通过 adb 设置 Credential Service (涉及Passkey)（小米HyperOS通用）

扫描此设备所有支持Credential Service的app及其完整组件名：

```
adb shell cmd package query-services --brief -a android.service.credentials.CredentialProviderService
```

查看当前Credential Service启用列表及首选：

注：credential_service为已开启服务列表（可以设置多个）；credential_service_primary为首选服务（建议只设置一个）。Android 14+支持多个凭据共存，如果启用了多个，它们会以冒号 `:` 分隔。

```
adb shell settings get secure credential_service
```
```
adb shell settings get secure credential_service_primary
```

更改当前Credential Service启用列表及首选（以Google密码管理器为例）：

建议：在 `settings put secure credential_service` 之后添加所有你需要开启的凭据服务（多个凭据服务名以冒号 `:` 分隔）；在 `adb shell settings put secure credential_service_primary` 之后只设置一个你优先选择的凭据服务。

```
adb shell settings put secure credential_service com.google.android.gms/com.google.android.gms.auth.api.credentials.credman.service.PasswordAndPasskeyService
```
```
adb shell settings put secure credential_service_primary com.google.android.gms/com.google.android.gms.auth.api.credentials.credman.service.PasswordAndPasskeyService
```

以下是一些已知的凭据服务名

- Google Play services（Google 密码管理器）：`com.google.android.gms/com.google.android.gms.auth.api.credentials.credman.service.PasswordAndPasskeyService`
- Bitwarden：`com.x8bit.bitwarden/com.x8bit.bitwarden.Autofill.CredentialProviderService`
- Microsoft Authenticator：`com.azure.authenticator/com.microsoft.authenticator.passkeys.businessLogic.AuthenticatorCredentialService`
- Microsoft Edge（Microsoft 密码管理器）：`com.microsoft.emmx/org.chromium.chrome.browser.webauthn.edge.PasskeyCredentialProviderService`

### HyperOS Passkey 特殊解决方案

卸载 HyperOS 系统自带的密码管理app：
```
adb shell pm uninstall --user 0 com.miui.passwords
```
```
adb shell pm uninstall --user 0 com.miui.password
```

如果有root，一个命令行解决通行密钥问题
```
adb shell pm uninstall --user 0 com.miui.contentcatcher
```

附个[Authnkey](https://f-droid.org/packages/pl.lebihan.authnkey/)，可能会修复小米手机的硬件通行密钥、Firefox通行密钥问题。

---

特别感谢↓

[通行密钥在 HyperOS 2 无法使用（已更新OS3）](https://web.vip.miui.com/page/info/mio/mio/detail?postId=47465346)

[小米澎湃OS4修改三方通行密钥App解决方案](https://www.lzskyline.com/archives/xiao-mi-peng-pai-os4xiu-gai-san-fang-tong-xing-mi-yao-appjie-jue-fang-an)

[修复小米 / HyperOS 无法弹出 Passkey 的问题](https://milu.ink/627.html)

[来自 ChaosWind - 酷安](https://www.coolapk.com/feed/70169697?s=ZGJmODk4NTMxMjNkMDJnNjk4ZGVjYmF6a1603)

<https://github.com/Kunzisoft/KeePassDX/issues/2220>

<https://github.com/Xposed-Modules-Repo/io.github.howard20181.hyperpasskey>

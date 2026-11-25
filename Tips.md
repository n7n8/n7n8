### 打开“Google设置”（小米、荣耀国行手机可能有麻烦）

adb命令行

```
adb shell am start -n com.google.android.gms/com.google.android.gms.app.settings.GoogleSettingsIALink
```

Package Name：`com.google.android.gms`

Activity：`com.google.android.gms.app.settings.GoogleSettingsIALink`

Activity启动器推荐：
<https://github.com/sdex/ActivityManager>

### CN-GMS 标签手机（Google Play无法自更新）更新 Google Play 入口

[APKMirror](https://www.apkmirror.com/apk/google-inc/google-play-store/)

[APKPure](https://apkpure.com/google-play-store-app/com.android.vending/amp)

### HyperOS 隐藏小白条
```
adb shell settings put global hide_gesture_line 1
```

### Android 调整字体粗细

以`-300`为例：

```
adb shell settings put secure font_weight_adjustment -300
```

### Android 手机 Material You 动态色彩设置（包括小米HyperOS）

[AOSP文档](https://source.android.com/docs/core/display/dynamic-color?hl=zh-cn)

调色板样式`android.theme.customization.theme_style`：

- `Tonal Spot`
- `Neutral`
- `Vibrant`
- `Expressive`
- `Rainbow`
- `FruitSalad`
- `Monochrome`
- `Fidelity`


以紫色`#746BC1`、调色板样式`VIBRANT`为例：

```
adb shell settings put secure theme_customization_overlay_packages '{"android.theme.customization.system_palette":"746BC1","android.theme.customization.accent_color":"746BC1","android.theme.customization.theme_style":"VIBRANT"}'
```

### Android 原生应用管理（实测HyperOS可以，ColorOS不行）

```
adb shell am start -n com.android.settings/.applications.ManageApplications
```

### 为单个 App 单独设置语言

[Android开发者文档](https://developer.android.com/reference/android/provider/Settings#ACTION_APP_LOCALE_SETTINGS)

1. 直接打开隐藏的“应用语言”设置页

如果只是想看看系统里是否藏着这个界面，可以用命令把它拉出来：

```
adb shell am start -a android.settings.APP_LOCALE_SETTINGS -d "<应用包名>"
```

把应用包名替换成你想改的 App（比如 `com.absinthe.libchecker` ）。如果系统支持，会直接跳到那个 App 的语言选择页。

2. 直接用命令设置语言
不依赖任何界面，直接告诉系统“这个 App 用哪种语言”：

```
adb shell cmd locale set-app-locales "应用包名" --locales "语言代码"
```

例如把某个 App 设为繁体中文：--locales "zh-TW"；恢复跟随系统则把 --locales 后面的值留空 ""。

### 隐藏“临时切横屏”按钮
```
adb shell settings put secure show_rotation_suggestions 0
```

### 三星浏览器改区

1. 打开
```
internet://debug
```
2. 打开“设置→Debug settings→Feature variation test”
3. 将Country code(China)、Country iso code(CN)改掉

---

### 微信 XWeb 微操

微信使用Android System Webview：
<http://debugxweb.qq.com/?reset_webview_kind_sys>

微信使用XWeb内核：
<http://debugxweb.qq.com/?reset_webview_kind_xweb>

微信更新体验版XWeb：
<http://debugxweb.qq.com/?set_config_url=https://dldir1v6.qq.com/weixin/android/wxweb/updateConfig_dev.xml&check_xwalk_update>

微信退回正式版XWeb：
<http://debugxweb.qq.com/?set_config_url=https://dldir1v6.qq.com/weixin/android/wxweb/updateConfig.xml&check_xwalk_update>

动态加载配置：

<https://dldir1v6.qq.com/weixin/android/wxweb/updateConfig_dev.xml> 开发版

<https://dldir1v6.qq.com/weixin/android/wxweb/updateConfig.xml> 正式版

---

### 特别感谢

[小米打开谷歌菜单](https://web.vip.miui.com/page/info/mio/mio/detail?app_version=dev.20051&postId=37895838)

[【DP】小米 17 Ultra 国行 HyperOS 4 如何关闭 Google 自动验证电话号码（设置里找不到Google的解决办法）](https://www.uscardforum.com/t/topic/542874)

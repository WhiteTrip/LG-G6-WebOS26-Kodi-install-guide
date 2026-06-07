# LG G6 / webOS 26 安装 Kodi IPK 教程

> 适用场景：部分较新的 LG 电视型号，例如 LG G6 系列和 webOS 26，目前可能无法通过 [webosbrew/dev-manager-desktop](https://github.com/webosbrew/dev-manager-desktop) 完成安装。本文提供两种替代方式：使用 Codex 客户端辅助安装，以及手动使用 LG 官方 webOS CLI 安装。

Kodi webOS nightly 安装包下载地址：

https://mirrors.kodi.tv/nightlies/webos/master/

LG 官方 webOS CLI Developer Guide：

https://webostv.developer.lge.com/develop/tools/cli-dev-guide

LG 官方 webOS CLI Installation：

https://webostv.developer.lge.com/develop/tools/cli-installation

---

## 一、使用 Codex 客户端辅助安装

这个方法适合不想自己手动输入一堆 CLI 命令的用户。你只需要在 Codex 客户端里告诉它电视 IP、Developer Mode app 显示的 passphrase，以及本地 IPK 文件路径，Codex 会在你的电脑上调用 webOS CLI 完成安装。

### 1. 准备工作

1. 电脑和 LG 电视连接到同一个局域网。
2. 在电视上安装并打开 **Developer Mode** app。
3. 使用 LG Developer 账号登录。
4. 打开 **Developer Mode**。
5. 按提示重启电视。
6. 重启后再次打开 **Developer Mode** app。
7. 记录电视显示的 IP 地址，例如 `192.168.10.17`。
8. 打开 **Key Server**。
9. 记录电视屏幕上显示的 6 位 **Passphrase**，例如 `ABC123`。
10. 从 Kodi nightly 页面下载 webOS 版 `.ipk` 文件到电脑。

Kodi 下载目录：

```text
https://mirrors.kodi.tv/nightlies/webos/master/
```

下载后你会得到类似这样的文件：

```text
org.xbmc.kodi_20260607-1b0eaf6a-master_arm.ipk
```

### 2. 在 Codex 客户端中让它帮你安装

打开 Codex 客户端，直接发送类似下面的消息：

```text
电视 IP 是 192.168.10.17，passphrase 是 ABC123，
我要安装的 IPK 在 G:\org.xbmc.kodi_20260607-1b0eaf6a-master_arm.ipk，
帮我安装到电视。
```

请把上面的 IP、passphrase 和 IPK 路径换成你自己的。

Codex 通常会帮你完成这些步骤：

```powershell
npm.cmd install -g @webos-tools/cli
ares.cmd -V
ares-setup-device.cmd --add myTV --info "host=192.168.10.17" --info "port=9922" --info "username=prisoner" --info "description=LG webOS TV"
ares-novacom.cmd --device myTV --getkey
ares-install.cmd --device myTV "G:\org.xbmc.kodi_20260607-1b0eaf6a-master_arm.ipk"
ares-install.cmd --device myTV --list
```

如果安装成功，终端会显示：

```text
Success
```

验证安装列表时应能看到：

```text
org.xbmc.kodi
```

### 3. 启动 Kodi

安装完成后，可以让 Codex 执行：

```powershell
ares-launch.cmd --device myTV org.xbmc.kodi
```

或者你也可以自己在 PowerShell 中运行这条命令。

### 4. 关于隐私

不要把真实 passphrase 发到公开论坛、Issue、评论区或截图里。它只应该在你自己的电脑和电视之间临时使用。分享教程时请使用 `ABC123`、`192.168.x.x` 这类占位符。

---

## 二、手动使用 webOS CLI 安装

下面是完全手动的安装方法，参考 LG 官方 webOS CLI 文档整理。

### 1. 安装 Node.js 和 webOS CLI

LG 官方文档建议的环境：

- Windows 10 或更高版本，64 位
- macOS 10.13 或更高版本，64 位
- Ubuntu 20.04 或更高版本，64 位
- Node.js 推荐版本：`14.15.1` 到 `16.20.2`
- npm

安装 webOS CLI：

```powershell
npm.cmd install -g @webos-tools/cli
```

检查 CLI 是否安装成功：

```powershell
ares.cmd -V
```

如果能看到类似下面的输出，说明 CLI 可用：

```text
Version: 3.2.4
```

Windows PowerShell 如果提示无法加载 `npm.ps1`，可以使用 `npm.cmd`，后续命令也可以使用 `.cmd` 形式，例如 `ares-install.cmd`。

### 2. 电视端开启 Developer Mode

1. 在 LG Content Store 中安装 **Developer Mode** app。
2. 打开 Developer Mode app，并登录 LG Developer 账号。
3. 打开 **Developer Mode**。
4. 按提示重启电视。
5. 重启后再次打开 Developer Mode app。
6. 记录电视 IP 地址。
7. 打开 **Key Server**。
8. 记录 6 位 passphrase。

电脑和电视必须在同一个局域网内。

### 3. 添加电视设备

把下面命令中的 `192.168.10.17` 换成你电视显示的 IP：

```powershell
ares-setup-device.cmd --add myTV --info "host=192.168.10.17" --info "port=9922" --info "username=prisoner" --info "description=LG webOS TV"
```

查看设备列表：

```powershell
ares-setup-device.cmd --list
```

正常情况下可以看到类似：

```text
myTV  prisoner@192.168.10.17:9922  ssh  tv
```

### 4. 获取电视 SSH key

确认电视 Developer Mode app 中的 **Key Server** 已开启，然后执行：

```powershell
ares-novacom.cmd --device myTV --getkey
```

根据提示输入电视上显示的 6 位 passphrase。

如果后续安装时报错：

```text
All configured authentication methods failed
```

可以手动绑定 key 和 passphrase：

```powershell
ares-setup-device.cmd --modify myTV --info "privatekey=myTV_webos" --info "passphrase=ABC123"
```

请把 `ABC123` 换成你的真实 passphrase。

### 5. 安装 Kodi IPK

从 Kodi 下载目录下载 webOS 版 `.ipk`：

```text
https://mirrors.kodi.tv/nightlies/webos/master/
```

假设你下载后的文件在 `G:\org.xbmc.kodi_20260607-1b0eaf6a-master_arm.ipk`，执行：

```powershell
ares-install.cmd --device myTV "G:\org.xbmc.kodi_20260607-1b0eaf6a-master_arm.ipk"
```

安装成功会显示：

```text
Success
```

### 6. 验证安装

```powershell
ares-install.cmd --device myTV --list
```

如果列表中出现下面的 app id，说明安装成功：

```text
org.xbmc.kodi
```

### 7. 启动或卸载 Kodi

启动 Kodi：

```powershell
ares-launch.cmd --device myTV org.xbmc.kodi
```

卸载 Kodi：

```powershell
ares-install.cmd --device myTV --remove org.xbmc.kodi
```

---

## 常见问题

### PowerShell 提示禁止运行脚本

如果看到类似 `无法加载 npm.ps1` 或 `running scripts is disabled`，可以改用：

```powershell
npm.cmd install -g @webos-tools/cli
```

后续命令也使用 `.cmd` 后缀：

```powershell
ares-install.cmd --device myTV --list
```

### 安装时提示认证失败

常见原因：

- 电视上的 **Key Server** 没有开启。
- passphrase 输入错误。
- Developer Mode 会话过期。
- `myTV` 设备配置里没有绑定 private key。

可以重新打开 Key Server 后执行：

```powershell
ares-novacom.cmd --device myTV --getkey
```

或者手动绑定：

```powershell
ares-setup-device.cmd --modify myTV --info "privatekey=myTV_webos" --info "passphrase=ABC123"
```

### Developer Mode 有效期

LG 电视的 Developer Mode 通常有有效期。快过期时，请打开电视上的 Developer Mode app 延长时间，否则后续安装和启动可能失败。

---

# LG G6 / webOS 26 Kodi IPK Installation Guide

> Use case: Some newer LG TV models, such as the LG G6 series and webOS 26 devices, may not currently work with [webosbrew/dev-manager-desktop](https://github.com/webosbrew/dev-manager-desktop). This guide provides two alternative installation methods: using the Codex desktop client to assist with installation, and manually installing through LG's official webOS CLI.

Kodi webOS nightly download directory:

https://mirrors.kodi.tv/nightlies/webos/master/

LG official webOS CLI Developer Guide:

https://webostv.developer.lge.com/develop/tools/cli-dev-guide

LG official webOS CLI Installation:

https://webostv.developer.lge.com/develop/tools/cli-installation

---

## 1. Install with Help from the Codex Client

This method is useful if you do not want to manually type every CLI command. You can provide Codex with your TV IP address, the passphrase shown in the Developer Mode app, and the local IPK file path. Codex can then run webOS CLI commands on your computer to install the package.

### 1.1 Preparation

1. Connect your computer and LG TV to the same local network.
2. Install and open the **Developer Mode** app on the TV.
3. Sign in with your LG Developer account.
4. Turn on **Developer Mode**.
5. Restart the TV when prompted.
6. Open the **Developer Mode** app again after the TV restarts.
7. Note the TV IP address, for example `192.168.10.17`.
8. Turn on **Key Server**.
9. Note the 6-character **Passphrase** shown on the TV, for example `ABC123`.
10. Download the webOS `.ipk` file from the Kodi nightly directory.

Kodi download directory:

```text
https://mirrors.kodi.tv/nightlies/webos/master/
```

The downloaded file may look like this:

```text
org.xbmc.kodi_20260607-1b0eaf6a-master_arm.ipk
```

### 1.2 Ask Codex to Install It

Open the Codex client and send a message like this:

```text
The TV IP is 192.168.10.17, the passphrase is ABC123,
and the IPK I want to install is at G:\org.xbmc.kodi_20260607-1b0eaf6a-master_arm.ipk.
Please install it on the TV.
```

Replace the IP address, passphrase, and IPK path with your own values.

Codex will usually run commands similar to these:

```powershell
npm.cmd install -g @webos-tools/cli
ares.cmd -V
ares-setup-device.cmd --add myTV --info "host=192.168.10.17" --info "port=9922" --info "username=prisoner" --info "description=LG webOS TV"
ares-novacom.cmd --device myTV --getkey
ares-install.cmd --device myTV "G:\org.xbmc.kodi_20260607-1b0eaf6a-master_arm.ipk"
ares-install.cmd --device myTV --list
```

If the installation succeeds, the terminal should show:

```text
Success
```

The installed app list should contain:

```text
org.xbmc.kodi
```

### 1.3 Launch Kodi

After installation, ask Codex to run:

```powershell
ares-launch.cmd --device myTV org.xbmc.kodi
```

You can also run the command yourself in PowerShell.

### 1.4 Privacy Note

Do not post your real passphrase in public forums, issues, comments, or screenshots. It should only be used locally between your own computer and TV. When sharing examples, use placeholders such as `ABC123` and `192.168.x.x`.

---

## 2. Manual Installation with webOS CLI

The following steps are based on LG's official webOS CLI documentation.

### 2.1 Install Node.js and webOS CLI

LG's official documentation recommends:

- Windows 10 or later, 64-bit
- macOS 10.13 or later, 64-bit
- Ubuntu 20.04 or later, 64-bit
- Node.js recommended versions: `14.15.1` to `16.20.2`
- npm

Install webOS CLI:

```powershell
npm.cmd install -g @webos-tools/cli
```

Check whether the CLI was installed successfully:

```powershell
ares.cmd -V
```

If you see output similar to this, the CLI is ready:

```text
Version: 3.2.4
```

If Windows PowerShell blocks `npm.ps1`, use `npm.cmd` instead. You can also use `.cmd` versions of the webOS CLI commands, such as `ares-install.cmd`.

### 2.2 Enable Developer Mode on the TV

1. Install the **Developer Mode** app from the LG Content Store.
2. Open the app and sign in with your LG Developer account.
3. Turn on **Developer Mode**.
4. Restart the TV when prompted.
5. Open the Developer Mode app again.
6. Note the TV IP address.
7. Turn on **Key Server**.
8. Note the 6-character passphrase.

Your computer and TV must be on the same local network.

### 2.3 Add the TV as a Target Device

Replace `192.168.10.17` with the IP address shown on your TV:

```powershell
ares-setup-device.cmd --add myTV --info "host=192.168.10.17" --info "port=9922" --info "username=prisoner" --info "description=LG webOS TV"
```

List configured devices:

```powershell
ares-setup-device.cmd --list
```

You should see something like:

```text
myTV  prisoner@192.168.10.17:9922  ssh  tv
```

### 2.4 Get the TV SSH Key

Make sure **Key Server** is enabled in the Developer Mode app, then run:

```powershell
ares-novacom.cmd --device myTV --getkey
```

Enter the 6-character passphrase shown on the TV when prompted.

If installation later fails with:

```text
All configured authentication methods failed
```

you can manually bind the key and passphrase:

```powershell
ares-setup-device.cmd --modify myTV --info "privatekey=myTV_webos" --info "passphrase=ABC123"
```

Replace `ABC123` with your real passphrase.

### 2.5 Install the Kodi IPK

Download the webOS `.ipk` file from:

```text
https://mirrors.kodi.tv/nightlies/webos/master/
```

Assume your downloaded file is located at `G:\org.xbmc.kodi_20260607-1b0eaf6a-master_arm.ipk`. Run:

```powershell
ares-install.cmd --device myTV "G:\org.xbmc.kodi_20260607-1b0eaf6a-master_arm.ipk"
```

If installation succeeds, you should see:

```text
Success
```

### 2.6 Verify the Installation

```powershell
ares-install.cmd --device myTV --list
```

If the following app id appears, Kodi is installed:

```text
org.xbmc.kodi
```

### 2.7 Launch or Remove Kodi

Launch Kodi:

```powershell
ares-launch.cmd --device myTV org.xbmc.kodi
```

Remove Kodi:

```powershell
ares-install.cmd --device myTV --remove org.xbmc.kodi
```

---

## FAQ

### PowerShell says script execution is disabled

If you see an error such as `cannot be loaded because running scripts is disabled`, use:

```powershell
npm.cmd install -g @webos-tools/cli
```

For later commands, use the `.cmd` form:

```powershell
ares-install.cmd --device myTV --list
```

### Installation fails with authentication errors

Common causes:

- **Key Server** is not enabled on the TV.
- The passphrase was entered incorrectly.
- The Developer Mode session expired.
- The `myTV` device configuration does not have a private key attached.

Turn on Key Server again and run:

```powershell
ares-novacom.cmd --device myTV --getkey
```

Or manually bind the key:

```powershell
ares-setup-device.cmd --modify myTV --info "privatekey=myTV_webos" --info "passphrase=ABC123"
```

### Developer Mode expiration

LG TV Developer Mode usually has an expiration timer. Open the Developer Mode app on the TV and extend the session before it expires, otherwise later installations or launches may fail.

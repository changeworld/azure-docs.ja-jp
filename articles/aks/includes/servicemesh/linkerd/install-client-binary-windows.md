---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: b7d832ba375925d30a976dfde63a776b5d0742bb
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593729"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Linkerd の linkerd クライアント バイナリをダウンロードしてインストールする

Windows の PowerShell ベースのシェルで `Invoke-WebRequest` を使用して、次のように Linkerd のリリースをダウンロードします。

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

`linkerd` クライアント バイナリはクライアント コンピューターで実行されます。このバイナリによって、Linkerd サービス メッシュの対話型操作が可能になります。 Windows で PowerShell ベースのシェルで Linkerd `linkerd` クライアント バイナリをインストールするには、次のコマンドを使用します。 これらのコマンドによって、Linkerd フォルダーに `linkerd` クライアント バイナリがコピーされ、即座 (現在のシェル内で) かつ永続的 (シェルの再起動により) に `PATH` 経由で利用できるようになります。 これらのコマンドの実行には昇格された (管理者) 特権は必要ありません。また、シェルを再起動する必要はありません。

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```
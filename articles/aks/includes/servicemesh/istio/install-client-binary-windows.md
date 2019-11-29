---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: a1a608b4f4e1afe4a3cf99229561a91cc3f9fa96
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170812"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Istio istioctl クライアント バイナリをダウンロードしてインストールする

Windows 上の PowerShell ベースのシェルでは、次のように `Invoke-WebRequest` を使用して Istio のリリースをダウンロードし、`Expand-Archive` で解凍します。

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.4.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

`istioctl` クライアント バイナリはクライアント コンピューターで実行されます。このバイナリによって、Istio サービス メッシュの対話型操作が可能になります。 Windows 上の Powershell ベースのシェルで次のコマンドを使用し、Istio `istioctl` クライアント バイナリをインストールします。 これらのコマンドによって、Istio フォルダーに `istioctl` クライアント バイナリがコピーされ、即座 (現在のシェル内で) かつ永続的 (シェルの再起動により) に `PATH` 経由で利用できるようになります。 これらのコマンドの実行には昇格された (管理者) 特権は必要ありません。また、シェルを再起動する必要はありません。

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```
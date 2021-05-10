---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: pgibson
ms.openlocfilehash: 8ac70027f7483fbf0131c31a5ba3631ed1d4ff9a
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079805"
---
## <a name="download-and-install-the-osm-client-binary"></a>OSM クライアント バイナリをダウンロードしてインストールする

Windows 上の PowerShell ベースのシェルでは、次のように `Invoke-WebRequest` を使用して Istio のリリースをダウンロードし、`Expand-Archive` で解凍します。

```powershell
# Specify the OSM version that will be leveraged throughout these instructions
$OSM_VERSION="v0.8.2"

[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-windows-amd64.zip&quot; -OutFile &quot;osm-$OSM_VERSION.zip"
Expand-Archive -Path "osm-$OSM_VERSION.zip" -DestinationPath .
```

`osm` クライアント バイナリはクライアント コンピューターで実行されます。これにより、AKS クラスターで OSM コントローラーを管理できます。 Windows 上の Powershell ベースのシェルで次のコマンドを使用して、OSM `osm` クライアント バイナリをインストールします。 これらのコマンドによって、OSM フォルダーに `osm` クライアント バイナリがコピーされ、(現在のシェル内で) 即時に、かつ (シェルの再起動をまたがって) 永続的に `PATH` 経由で利用できるようになります。 これらのコマンドの実行には昇格された (管理者) 特権は必要ありません。また、シェルを再起動する必要はありません。

```powershell
# Copy osm.exe to C:\OSM
New-Item -ItemType Directory -Force -Path "C:\OSM"
Move-Item -Path .\windows-amd64\osm.exe -Destination "C:\OSM\"

# Add C:\OSM to PATH.
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH&quot;, &quot;User&quot;) + &quot;;C:\OSM\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\OSM\"
```

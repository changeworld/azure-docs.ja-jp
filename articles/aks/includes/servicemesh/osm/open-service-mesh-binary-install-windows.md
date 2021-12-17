---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: pgibson
ms.openlocfilehash: 10759b4d80b39b1fc9a04d513e75d98b7c691281
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440100"
---
## <a name="download-and-install-the-open-service-mesh-osm-client-binary"></a>Open Service Mesh (OSM) クライアント バイナリをダウンロードしてインストールする

Windows 上の PowerShell ベースのシェルでは、次のように `Invoke-WebRequest` を使用して OSM のリリースをダウンロードし、`Expand-Archive` で解凍します。

```powershell
# Specify the OSM version that will be leveraged throughout these instructions
$OSM_VERSION="v0.9.1"

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

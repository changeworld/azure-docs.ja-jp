---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: phillipgibson
ms.openlocfilehash: f99db628b08084ca750816c00a6892e877e90efc
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079804"
---
## <a name="download-and-install-the-osm-client-binary"></a>OSM クライアント バイナリをダウンロードしてインストールする

Linux または [Linux 用 Windows サブシステム][install-wsl]上の bash ベースのシェルで、次のように `curl` を使用して OSM のリリースをダウンロードし、`tar` で解凍します。

```bash
# Specify the OSM version that will be leveraged throughout these instructions
OSM_VERSION=v0.8.2

curl -sL "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-linux-amd64.tar.gz" | tar -vxzf -
```

`osm` クライアント バイナリはクライアント コンピューターで実行されます。これを使用すると、AKS クラスターで OSM を管理できます。 Linux または [Linux 用 Windows サブシステム][install-wsl]上の bash ベースのシェルで OSM `osm` クライアント バイナリをインストールするには、次のコマンドを使用します。 これらのコマンドによって、`osm` の標準ユーザー プログラムの場所に `PATH` クライアント バイナリがコピーされます。

```bash
sudo mv ./linux-amd64/osm /usr/local/bin/osm
sudo chmod +x /usr/local/bin/osm
```

次のコマンドを使用して、`osm` クライアント ライブラリがパスに正しく追加されていることと、そのバージョン番号を確認できます。

```
osm version
```

<!-- LINKS - external -->

[install-wsl]: /windows/wsl/install-win10

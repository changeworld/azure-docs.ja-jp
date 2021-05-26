---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: phillipgibson
ms.openlocfilehash: 6a4a3ee151c75afcd213dd1bac6d451c8857c4e7
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110468099"
---
## <a name="download-and-install-the-osm-client-binary"></a>OSM クライアント バイナリをダウンロードしてインストールする

bash ベースのシェルで、次のように `curl` を使用して OSM のリリースをダウンロードし、`tar` で解凍します。

```bash
# Specify the OSM version that will be leveraged throughout these instructions
OSM_VERSION=v0.8.4

curl -sL "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-darwin-amd64.tar.gz" | tar -vxzf -
```

`osm` クライアント バイナリはクライアント コンピューターで実行されます。これを使用すると、AKS クラスターで OSM を管理できます。 bash ベースのシェルで次のコマンドを使用して、OSM `osm` クライアント バイナリをインストールします。 これらのコマンドによって、`osm` の標準ユーザー プログラムの場所に `PATH` クライアント バイナリがコピーされます。

```bash
sudo mv ./darwin-amd64/osm /usr/local/bin/osm
sudo chmod +x /usr/local/bin/osm
```

次のコマンドを使用して、`osm` クライアント ライブラリがパスに正しく追加されていることと、そのバージョン番号を確認できます。

```
osm version
```

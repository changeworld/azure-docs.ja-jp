---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8b1a4a8a6c808348ca17a9eebac17c5821ceefe5
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529735"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Linkerd の linkerd クライアント バイナリをダウンロードしてインストールする

MacOS の bash ベースのシェルで `curl` を使用して、次のように Linkerd のリリースをダウンロードします。

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

`linkerd` クライアント バイナリはクライアント コンピューターで実行されます。このバイナリによって、Linkerd サービス メッシュの対話型操作が可能になります。 MacOS で bash ベースのシェルで Linkerd `linkerd` クライアント バイナリをインストールするには、次のコマンドを使用します。 これらのコマンドによって、`linkerd` の標準ユーザー プログラムの場所に `PATH` クライアント バイナリがコピーされます。

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Linkerd `linkerd` クライアント バイナリに対してコマンドラインで補完する場合、次のように設定します。

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```
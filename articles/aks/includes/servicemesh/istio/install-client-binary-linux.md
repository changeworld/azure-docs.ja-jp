---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: b310de560f9791e1fc49d54dfbf0789c38d37f57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593985"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Istio istioctl クライアント バイナリをダウンロードしてインストールする

Linux または [Windows Subsystem for Linux][install-wsl] 上の bash ベースのシェルで、次のように `curl` を使用して Istio のリリースをダウンロードし、`tar` で解凍します。

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

`istioctl` クライアント バイナリはクライアント コンピューターで実行されます。このバイナリによって、Istio サービス メッシュの対話型操作が可能になります。 Linux または `istioctl`Windows Subsystem for Linux[ で bash ベースのシェルで Istio ][install-wsl] クライアント バイナリをインストールするには、次のコマンドを使用します。 これらのコマンドによって、`istioctl` の標準ユーザー プログラムの場所に `PATH` クライアント バイナリがコピーされます。

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Istio `istioctl` クライアント バイナリに対してコマンドラインで補完する場合、次のように設定します。

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10
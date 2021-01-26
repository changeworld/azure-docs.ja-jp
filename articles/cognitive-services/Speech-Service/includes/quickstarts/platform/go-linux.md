---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: f674edd15b86f49d60450a53f5df5852b32f95a4
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906438"
---
このガイドでは、Linux 用 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) をインストールする方法について説明します

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>システム要件

Linux。[サポートされている Linux ディストリビューションとターゲット アーキテクチャ](~/articles/cognitive-services/speech-service/speech-sdk.md)の一覧を参照してください。

## <a name="prerequisites"></a>前提条件

このクイックスタートを完了するには、次のものが必要です。

* gcc
* [Go バイナリ (1.13 移行)](https://golang.org/dl/)

* サポートされている Linux プラットフォームでは、特定のライブラリがインストールされている必要があります (Secure Sockets Layer サポート用に `libssl`、サウンド サポート用に `libasound2`)。 これらのライブラリの正しいバージョンをインストールするために必要なコマンドについては、下記のディストリビューションを参照してください。

   * Ubuntu/Debian の場合:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

     libssl1.0.0 が使用できない場合は、libssl1.0.x (ここで、x は 0 より大) または libssl1.1 を代わりにインストールします。

   * RHEL または CentOS の場合:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> - RHEL または CentOS 7 の場合、「[Speech SDK 用に RHEL/CentOS 7 を構成する](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)」の手順に従います。
> - RHEL または CentOS 8 の場合、「[Linux 用 OpenSSL の構成](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)」の手順に従います。

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]


## <a name="configure-go-environment"></a>Go 環境を構成する

次の手順を実行して、Speech SDK を検索するための Go 環境を設定します。 両方の手順で、`<architecture>` を CPU のプロセッサ アーキテクチャに置き換えます。 これは `x86`、`x64`、`arm32`、`arm64` のいずれかになります。

1. バインドは `cgo` に依存するため、Go が SDK を検出できるように環境変数を設定する必要があります。

   ```sh
   export CGO_CFLAGS="-I$SPEECHSDK_ROOT/include/c_api"
   export CGO_LDFLAGS="-L$SPEECHSDK_ROOT/lib/<architecture> -lMicrosoft.CognitiveServices.Speech.core"
   ```

1. その SDK を含むアプリケーションを実行するには、ライブラリの検索先を OS に伝える必要があります。

   ```sh
   export LD_LIBRARY_PATH="$SPEECHSDK_ROOT/lib/<architecture>:$LD_LIBRARY_PATH"
   ```

## <a name="next-steps"></a>次のステップ

[!INCLUDE [windows](../quickstart-list-go.md)]

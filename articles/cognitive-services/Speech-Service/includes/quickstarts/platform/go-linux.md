---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: 9efcbb7b42fd9bc2a44211b765dfd9254623d5ce
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252231"
---
このガイドでは、Linux 上の GO 用 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) をインストールする方法について説明します。 

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>システム要件

Linux。[サポートされている Linux ディストリビューションとターゲット アーキテクチャ](~/articles/cognitive-services/speech-service/speech-sdk.md)の一覧を参照してください。

## <a name="prerequisites"></a>前提条件

このクイックスタートを完了するには、次のものが必要です。

* [Go バイナリ (1.13 移行)](https://golang.org/dl/)
* Linux 環境は、[システム要件とセットアップ手順](~/articles/cognitive-services/speech-service/speech-sdk.md#get-the-speech-sdk)に従って設定されます。


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

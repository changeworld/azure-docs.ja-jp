---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5a06a0663601c221dd456b9cf4437cb9f32a18f8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399899"
---
:::row:::
    :::column span="3":::
        C++ Speech SDK は Windows, Linux, macOSで使用できます。 詳細については、 <a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">Microsoft.CognitiveServices.Speech <span class="docon docon-navigate-external x-hidden-focus"> </span></a>を参照してください。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>C++ NuGet パッケージ

C++ Speech SDK は**パッケージマネージャー**から以下の`Install-Package`コマンドでインストールができます。

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>C++バイナリファイルとヘッダーファイル

または、 C++ Speech SDK をバイナリからインストールすることもできます。 <a href="https://aka.ms/csspeech/linuxbinary" target="_blank">.tarパッケージ<span class="docon docon-navigate-external x-hidden-focus"></span></a>としてSDKをダウンロードし、ファイルを任意のディレクトリにアンパックします。 このパッケージ (x86 と x64 の両方のターゲットアーキテクチャのヘッダーファイルを含む) の内容は、次のように構成されています：

  | Path                   | 説明                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | ライセンス                                              |
  | `ThirdPartyNotices.md` | サード パーティに関する通知                                  |
  | `include`              | C++ヘッダーファイル                                 |
  | `lib/x64`              | アプリケーションとのリンクのためのネイティブ x64 ライブラリ |
  | `lib/x86`              | アプリケーションとのリンクのためのネイティブ x86 ライブラリ |

  アプリケーションを作成するには、必須のバイナリ (およびライブラリ) を開発環境にコピーまたは移動し、 必要に応じてそれらをビルド プロセスに含めます。

#### <a name="additional-resources"></a>その他のリソース

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">Windows、Linux、macOS のクイックC++スタートソースコード<span class="docon docon-navigate-external x-hidden-focus"></span></a>
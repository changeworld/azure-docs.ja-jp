---
title: Speech CLI の構成オプション - 音声サービス
titleSuffix: Azure Cognitive Services
description: Azure Speech CLI で使用する構成ファイルを作成して管理する方法について説明します。
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: eur
ms.openlocfilehash: 6b830d9afd3177aaa9efd0effb7fa8d3fd2e46dc
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719924"
---
# <a name="speech-cli-configuration-options"></a>Speech CLI の構成オプション

Speech CLI の動作は、`@` 記号を使用して参照できる構成ファイルの設定に依存することができます。 Speech CLI では、新しい設定は、Speech CLI 用に現在の作業ディレクトリ内に作成される新しい `./spx/data` サブディレクトリに保存されます。 Speech CLI では、構成値を探すときに、現在の作業ディレクトリ、`./spx/data` のデータストア、`spx` バイナリの最後の読み取り専用データストアを含むその他のデータストアの順に検索が行われます。 

Speech CLI のクイックスタートでは、データストアを使用して `@key` 値と `@region` 値を保存したため、各 `spx` コマンドでそれらを指定する必要はありませんでした。 構成ファイルを使用して独自の構成設定を保存したり、これらを使用して実行時に生成された URL やその他の動的コンテンツを渡したりできることを覚えておいてください。

> [!NOTE]
> PowerShell では、[解析中止トークン](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_special_characters?view=powershell-7.2#stop-parsing-token---) (`--%`) は `spx` に続ける必要があります。 たとえば、`spx --% config @region` を実行すると、現在のリージョンの構成値が表示されます。

## <a name="create-and-manage-configuration-files-in-the-datastore"></a>データストア内に構成ファイルを作成して管理する

このセクションでは、ローカル データストア内の構成ファイルを使用し、`spx config` を使用してコマンド設定の格納とフェッチを行い、`--output` オプションを使用して Speech CLI からの出力を格納する方法を示します。

次の例では、`@my.defaults` 構成ファイルをクリアし、ファイル内の **key** と **region** のキーと値のペアを追加し、`spx recognize` の呼び出しでこの構成を使用します。

```console
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

構成ファイルに動的コンテンツを書き込むこともできます。 たとえば、次のコマンドでは、カスタム音声モデルを作成し、新しいモデルの URL を構成ファイルに格納しています。 次のコマンドでは、その URL のモデルが使用できる状態になるまで待機してから制御を返します。

```console
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

次の例では、2 つの URL を `@my.datasets.txt` 構成ファイルに書き込みます。 このシナリオでは、`--output` にオプションの **add** キーワードを含めることによって、構成ファイルを作成したり、既存の構成ファイルに追加したりできます。


```console
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

既定の構成ファイル (コマンドに固有の既定の設定の場合は `@spx.default`、`@default.config`、および `@*.default.config`) の使用法など、データストア ファイルの詳細については、次のコマンドを入力してください。

```console
spx help advanced setup
```

## <a name="next-steps"></a>次のステップ 

* [Speech CLI を使用したバッチ操作](./spx-batch-operations.md)

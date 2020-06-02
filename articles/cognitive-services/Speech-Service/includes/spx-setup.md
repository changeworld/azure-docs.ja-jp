---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: c57187ae15e5f15a601edbc74921b3c879abe715
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800697"
---
## <a name="prerequisites"></a>前提条件

このチュートリアルの唯一の前提条件は、Azure Speech サブスクリプションです。 まだお持ちでない場合は、新しいサブスクリプションの作成に関する[ガイド](../get-started.md#new-resource)を参照してください。

## <a name="download-and-install"></a>ダウンロードしてインストールする

#### <a name="windows-install"></a>[Windows のインストール](#tab/windowsinstall)

Windows に Speech CLI をインストールするには、次の手順に従います。

1. [.NET Framework 4.7](https://dotnet.microsoft.com/download/dotnet-framework/net471) または [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0) のいずれかをインストールします。
2. Speech CLI [ZIP アーカイブ](https://aka.ms/speech/spx-zips.zip)をダウンロードして、抽出します。
3. ダウンロードから抽出したルート ディレクトリ `spx-zips` にアクセスし、必要なサブディレクトリを抽出します (.NET Framework 4.7 の場合は `spx-net471`、x64 CPU での .NET Core 3.0 の場合は `spx-netcore-win-x64`)。

コマンド プロンプトで、ディレクトリをこの場所に変更し、「`spx`」と入力して、Speech CLI のヘルプを表示します。

#### <a name="linux-install"></a>[Linux のインストール](#tab/linuxinstall)

Linux の x64 CPU で Speech CLI をインストールするには、次の手順に従います。

1. [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0) をインストールします。
2. Speech CLI [ZIP アーカイブ](https://aka.ms/speech/spx-zips.zip)をダウンロードして、抽出します。
3. ダウンロードから抽出したルートディレクトリ `spx-zips` にアクセスし、`spx-netcore-30-linux-x64` を新しい `~/spx` ディレクトリに抽出します。
4. ターミナルで、次のコマンドを入力します。
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

「`spx`」と入力して、Speech CLI のヘルプを表示します。

***

## <a name="create-subscription-config"></a>サブスクリプション構成の作成

Speech CLI の使用を開始するには、最初に、Speech サブスクリプション キーとリージョン情報を入力する必要があります。 リージョン識別子を確認するには、[リージョンのサポート](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)に関するページを参照してください。 サブスクリプション キーとリージョン識別子 (たとえば、 `eastus`、`westus`) を入手したら、次のコマンドを実行します。

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

これで、今後の SPX 要求のためのサブスクリプション認証が格納されるようになりました。 これらの格納されている値のいずれかを削除する必要がある場合は、`spx config @region --clear` または `spx config @key --clear` を実行します。

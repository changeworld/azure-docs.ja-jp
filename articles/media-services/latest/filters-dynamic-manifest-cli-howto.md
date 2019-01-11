---
title: Azure Media Services で CLI 使用してフィルターを作成する | Microsoft Docs
description: このトピックでは、Media Services で CLI を使用してフィルターを作成する方法について説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 471277433f0fc9a54a28baa158f1e20f1efb613f
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000521"
---
# <a name="creating-filters-with-cli"></a>CLI を使用してフィルターを作成する 

コンテンツを顧客に配信 (ライブ イベントやビデオ オン デマンドをストリーム配信) する際、資産の既定のマニフェスト ファイルに記述された内容だけではクライアントのニーズに柔軟に対応できない場合があります。 Azure Media Services では、アカウント フィルターと、コンテンツの資産フィルターを定義することができます。 詳細については、「 [フィルターと動的マニフェスト](filters-dynamic-manifest-overview.md)」を参照してください。

このトピックでは、ビデオ オン デマンド資産用のフィルターを構成し、Media Services v3 用の CLI を使用して[アカウント フィルター](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)と[資産 フィルター](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)を作成する方法について説明します。 

## <a name="prerequisites"></a>前提条件 

- CLI をローカルにインストールして使用します。この記事では、Azure CLI バージョン 2.0 以降が必要です。 お使いのバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

    現在、一部の [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) コマンドが Azure Cloud Shell では正常に動作しません。 CLI はローカルで使用することをお勧めします。
- [Media Services アカウントを作成する](create-account-cli-how-to.md) リソース グループ名と Media Services アカウント名を覚えておいてください。 
- [フィルターと動的マニフェスト](filters-dynamic-manifest-overview.md)を確認します。

## <a name="define-a-filter"></a>フィルターの定義 

次に、最終的なマニフェストに追加されるトラック選択条件を定義する例を示します。 このフィルターには、EC-3 のオーディオ トラックと、0 から 1,000,000 の範囲のビットレートのビデオ トラックが含まれます。

REST で定義されているフィルターには、"プロパティ" ラッパーの JSON オブジェクトが含まれます。  

```json
[
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Audio",
                "operation": "Equal"
            },
            {
                "property": "FourCC",
                "value": "EC-3",
                "operation": "NotEqual"
            }
        ]
    },
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Video",
                "operation": "Equal"
            },
            {
                "property": "Bitrate",
                "value": "0-1000000",
                "operation": "Equal"
            }
        ]
    }
]
```

## <a name="create-account-filters"></a>アカウント フィルターの作成

次の [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) コマンドでは、[前に定義した](#define-a-filter)フィルター トラック選択を含むアカウント フィルターが作成されます。 

このコマンドを使用すると、トラック選択を表す JSON を含むオプションの `--tracks` パラメーターを渡すことができます。  ファイルから JSON を読み込むには、@ {ファイル} を使用します。 Azure CLI をローカルで使用している場合は、ファイルのパス全体を指定します。


```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @c:\tracks.json
```

Azure Cloud Shell を使用している場合は、Cloud Shell にファイルをアップロードします (シェル ウィンドウの上部にあるファイルのアップロード/ダウンロード用ボタンを探します)。 その後は、ファイルを次のように参照できます。

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

[フィルターに関する JSON の例](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter)も参照してください。

## <a name="create-asset-filters"></a>資産フィルターの作成

次の [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) コマンドでは、[前に定義した](#define-a-filter)フィルター トラック選択を含む資産フィルターが作成されます。 

> [!TIP]
> 前のセクションで、ファイル名の場所の指定に関する情報を参照してください。

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

[フィルターに関する JSON の例](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter)も参照してください。

## <a name="next-step"></a>次のステップ

[ビデオのストリーム配信](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>関連項目

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

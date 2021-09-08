---
title: GitHub に接続する
titleSuffix: Azure Data Factory & Azure Synapse
description: GitHub を使用して Common Data Model エンティティ参照を指定する
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: jingwang
ms.openlocfilehash: 8d765a5150180303f55cc669da3ddb84deea68da
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123312902"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>GitHub を使用して Common Data Model エンティティ参照を読み取る

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory の GitHub コネクタは、マッピング データ フローで [Common Data Model](format-common-data-model.md) 形式のエンティティ参照スキーマを受け取る目的でのみ使用されます。

## <a name="create-a-linked-service-to-github-using-ui"></a>UI を使用して GitHub のリンク サービスを作成する

次の手順を使用して、Azure portal UI で GitHub のリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンクされたサービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI を使用した新しいリンク サービスの作成を示すスクリーンショット。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用した新しいリンク サービスの作成を示すスクリーンショット。":::

2. GitHub を検索し、GitHub コネクタを選択します。

   :::image type="content" source="media/connector-github/github-connector.png" alt-text="GitHub コネクタのスクリーンショット。":::    


1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

   :::image type="content" source="media/connector-github/configure-github-linked-service.png" alt-text="GitHub のリンク サービスの構成のスクリーンショット。":::


## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

GitHub のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **GitHub** に設定する必要があります。 | はい
| userName | GitHub ユーザー名 | はい |
| password | GitHub パスワード | はい |

## <a name="next-steps"></a>次の手順

マッピング データ フローで[ソース データセット](data-flow-source.md)を作成します。
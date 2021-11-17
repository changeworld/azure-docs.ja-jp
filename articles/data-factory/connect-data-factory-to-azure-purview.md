---
title: Data Factory を Azure Purview に接続する
description: Data Factory を Azure Purview に接続する方法について説明します
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019, references_regions
ms.date: 10/25/2021
ms.openlocfilehash: 026dc4cb24c5e7b51fc5eec918cf92d3b6b23090
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846142"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Data Factory を Azure Purview に接続する (プレビュー)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[Azure Purview](../purview/overview.md) は、オンプレミス、マルチクラウド、SaaS (サービスとしてのソフトウェア) にあるデータの管理と制御を支援する統合データ ガバナンス サービスです。 データ ファクトリを Azure Purview に接続できます。 この接続により、Azure Purview を使用して、データ系列データをキャプチャでき、Azure Purview 資産を検出して探索することもできます。

## <a name="connect-data-factory-to-azure-purview"></a>Data Factory を Azure Purview に接続する

Data Factory を Azure Purview に接続するには、次の 2 つのオプションがあります。

- [Azure Purview アカウントに Data Factory で接続する](#connect-to-azure-purview-account-in-data-factory)
- [Azure Purview に Data Factory を登録する](#register-data-factory-in-azure-purview)

### <a name="connect-to-azure-purview-account-in-data-factory"></a>Azure Purview アカウントに Data Factory で接続する

Azure Purview アカウントに接続するには、データ ファクトリで **所有者** または **共同作成者** のロールが必要です。

Data Factory 作成 UI で接続を確立するには

1. ADF 作成 UI で、 **[管理]**  ->  **[Azure Purview]** に移動し、 **[Connect to a Purview account]\(Purview アカウントに接続する\)** を選択します。 

    :::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="Purview アカウントを登録するためのスクリーンショット。":::

2. **[Azure サブスクリプションから]** または **[手動で入力]** を選択します。 **Azure サブスクリプションから**、自分がアクセスできるアカウントを選択できます。

3. 接続すると、 **[Purview アカウント]** タブで Purview アカウントの名前を確認できます。

Purview アカウントがファイアウォールによって保護されている場合は、Purview 用のマネージド プライベート エンドポイントを作成します。 Data Factory から[セキュリティで保護された Purview アカウントにアクセス](how-to-access-secured-purview-account.md)できるようにする方法の詳細を確認してください。 初期接続時にそれを行うことも、既存の接続を後で編集することもできます。

Purview の接続情報は、次のようなデータ ファクトリ リソースに格納されます。 プログラムを使用して接続を確立するには、データ ファクトリを更新し、`purviewConfiguration` 設定を追加します。 SSIS アクティビティから系列をプッシュする必要がある場合は、さらに `catalogUri` タグも追加します。

```json
{
    "name": "ContosoDataFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>",
    "properties": {
        ...
        "purviewConfiguration": {
            "purviewResourceId": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupname>/providers/Microsoft.Purview/accounts/<PurviewAccountName>"
        }
    },
    ...
    "identity": {...},
    "tags": {
        "catalogUri": "<PurviewAccountName>.purview.azure.com/catalog //Note: used for SSIS lineage only"
    }
}
```

### <a name="register-data-factory-in-azure-purview"></a>Azure Purview に Data Factory を登録する

Azure Purview に Data Factory を登録する方法については、「[Azure Data Factory と Azure Purview を接続する方法](../purview/how-to-link-azure-data-factory.md)」を参照してください。

## <a name="set-up-authentication"></a>認証の設定

データ ファクトリのマネージド ID は、データ ファクトリから Purview への系列のプッシュ操作を認証するために使用されます。 

データ ファクトリのマネージド ID に、Purview **ルート コレクション** の **データ キュレーター** ロールを付与します。 詳細については、[Azure Purview でのアクセス制御](../purview/catalog-permissions.md)および[コレクションを使用したロールの追加とアクセスの制限](../purview/how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections)に関連するページを参照してください。

作成 UI でデータ ファクトリを Purview に接続すると、ADF はこのようなロールの割り当てを自動的に追加します。 Purview ルート コレクションに対する **コレクション管理者** ロールがあり、ネットワークから Purview アカウントにアクセスできる場合、この操作は成功します。

## <a name="monitor-purview-connection"></a>Purview 接続を監視する

データ ファクトリを Purview アカウントに接続すると、次のページが表示されます。このページには、有効な統合機能の詳細が示されています。

:::image type="content" source="./media/data-factory-purview/monitor-purview-connection-status.png" alt-text="Azure Data Factory と Purview の統合状態を監視するためのスクリーンショット。":::

**データ系列 - パイプライン** については、次のいずれかの状態が表示されます。

- **接続中**: データ ファクトリは Purview アカウントに正常に接続されています。 これは、データ ファクトリが Purview アカウントに関連付けられていて、そのアカウントに系列をプッシュする権限が、そのデータ ファクトリにあることを示しています。 Purview アカウントがファイアウォールによって保護されている場合は、アクティビティの実行および系列のプッシュに使用される統合ランタイムが、Purview アカウントに到達できることを確認する必要もあります。 詳細については、「[保護された Azure Purview アカウントに Azure Data Factory でアクセスする](how-to-access-secured-purview-account.md)」を参照してください。
- **接続解除**: Purview データ キュレーター ロールがデータ ファクトリのマネージド ID に付与されないため、データ ファクトリは、Purview に系列をプッシュできません。 この問題を修正するには、ご自身の Purview アカウントに移動してロールの割り当てを確認し、必要に応じて手動でロールを付与します。 詳細については、「[認証の設定](#set-up-authentication)」セクションを 参照してください。
- **不明**: Data Factory によって状態を確認できません。 次のような原因が考えられます。

    - アカウントがファイアウォールによって保護されているため、ご自身の現在のネットワークから Purview アカウントに到達できません。 代わりに、ご自身の Purview アカウントに接続されているプライベート ネットワークから ADF UI を起動できます。
    - Purview アカウント上でロールの割り当てを確認する権限がありません。 ご自身のロールの割り当てについては、Purview アカウント管理者に問い合わせることができます。 必要な Purview ロールについては、「[認証の設定](#set-up-authentication)」セクションを参照してください。

## <a name="report-lineage-data-to-azure-purview"></a>データ系列データを Azure Purview に報告する

データ ファクトリを Purview アカウントに接続すると、パイプラインを実行するときに、Data Factory が Purview アカウントに系列情報をプッシュします。 サポートされている機能の詳細については、「[サポートされる Azure Data Factory アクティビティ](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities)」を参照してください。 エンドツーエンドのチュートリアルについては、「[チュートリアル: Data Factory 系列データを Azure Purview にプッシュする](tutorial-push-lineage-to-purview.md)」を参照してください。

## <a name="discover-and-explore-data-using-purview"></a>Purview を使用してデータを検出して探索する

データ ファクトリを Purview アカウントに接続すると、Data Factory 作成 UI の上部中央にある検索バーを使用して、データを検索してアクションを実行できます。 詳細については、「[Purview を使用した ADF でのデータの検出と探索](how-to-discover-explore-purview-data.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

[チュートリアル: Data Factory 系列データを Azure Purview にプッシュする](tutorial-push-lineage-to-purview.md)

[Purview を使用した ADF でのデータの検出と探索](how-to-discover-explore-purview-data.md)

[セキュリティで保護された Azure Purview アカウントにアクセスする](how-to-access-secured-purview-account.md)

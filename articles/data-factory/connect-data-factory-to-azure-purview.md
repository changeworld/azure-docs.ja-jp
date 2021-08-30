---
title: Data Factory を Azure Purview に接続する
description: Data Factory を Azure Purview に接続する方法について説明します
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019, references_regions
ms.date: 08/10/2021
ms.openlocfilehash: 1d03eb65ebc976691ee76dc4dd208baa7445d92e
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2021
ms.locfileid: "122201633"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Data Factory を Azure Purview に接続する (プレビュー)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[Azure Purview](../purview/overview.md) は、オンプレミス、マルチクラウド、SaaS (サービスとしてのソフトウェア) にあるデータの管理と制御を支援する統合データ ガバナンス サービスです。 データ ファクトリを Azure Purview に接続できます。 この接続により、Azure Purview を使用して、データ系列データをキャプチャできるだけでなく、Azure Purview 資産を検出して探索することもできます。

## <a name="connect-data-factory-to-azure-purview"></a>Data Factory を Azure Purview に接続する

Data Factory を Azure Purview に接続するには、次の 2 つのオプションがあります。

- [Azure Purview アカウントに Data Factory で接続する](#connect-to-azure-purview-account-in-data-factory)
- [Azure Purview に Data Factory を登録する](#register-data-factory-in-azure-purview)

### <a name="connect-to-azure-purview-account-in-data-factory"></a>Azure Purview アカウントに Data Factory で接続する

接続を確立するには、Data Factory で **所有者** ロールまたは **共同作成者** ロールを持っている必要があります。

1. ADF 作成 UI で、 **[管理]**  ->  **[Azure Purview]** に移動し、 **[Connect to a Purview account]\(Purview アカウントに接続する\)** を選択します。 

    :::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="Purview アカウントを登録するためのスクリーンショット。":::

2. **[Azure サブスクリプションから]** または **[手動で入力]** を選択します。 **Azure サブスクリプションから**、自分がアクセスできるアカウントを選択できます。

3. 接続すると、 **[Purview アカウント]** タブで Purview アカウントの名前を確認できます。

データ ファクトリを Purview に接続すると、ADF UI は、Purview アカウントでデータ ファクトリのマネージド ID **Purview データ キュレーター** ロールの付与も試みます。 マネージド ID は、データ ファクトリから Purview へのデータ系列プッシュ操作を認証するために使用されます。 Purview アカウントに **所有者** ロールまたは **ユーザー アクセス管理者** ロールがある場合、この操作は自動的に行われます。 表示されない場合は、次のような警告が表示されます。

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="Purview アカウント登録の警告のスクリーンショット。":::

この問題を解決するには、Azure portal -> Purview アカウント -> Access Control (IAM) に移動し、**Purview データ キュレーター** ロールがデータ ファクトリのマネージド ID に付与されていないか確認します。 ない場合は、ロールの割り当てを手動で追加します。

### <a name="register-data-factory-in-azure-purview"></a>Azure Purview に Data Factory を登録する

Azure Purview に Data Factory を登録する方法については、「[Azure Data Factory と Azure Purview を接続する方法](../purview/how-to-link-azure-data-factory.md)」を参照してください。 

## <a name="report-lineage-data-to-azure-purview"></a>データ系列データを Azure Purview に報告する

データ ファクトリを Purview アカウントに接続した後、コピー、データ フロー、または SSIS パッケージの実行アクティビティを実行すると、データ プロセスによって作成されたデータセット間のデータ系列を取得し、データ ソースとターゲット間のワークフロー プロセス全体の概要を取得できます。 サポートされている機能の詳細については、「[サポートされる Azure Data Factory アクティビティ](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities)」を参照してください。 エンドツーエンドのチュートリアルについては、「[チュートリアル: Data Factory 系列データを Azure Purview にプッシュする](tutorial-push-lineage-to-purview.md)」を参照してください。

## <a name="discover-and-explore-data-using-purview"></a>Purview を使用してデータを検出して探索する

データ ファクトリを Purview アカウントに接続した後、Azure Data Factory 作成 UI の上部中央にある検索バーを使用して、データを検索してアクションを実行できます。 詳細については、「[Purview を使用した ADF でのデータの検出と探索](how-to-discover-explore-purview-data.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

[チュートリアル: Data Factory 系列データを Azure Purview にプッシュする](tutorial-push-lineage-to-purview.md)

[Purview を使用した ADF でのデータの検出と探索](how-to-discover-explore-purview-data.md)

[Azure Purview Data Catalog の系列のユーザー ガイド](../purview/catalog-lineage-user-guide.md)
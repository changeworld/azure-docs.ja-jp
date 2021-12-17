---
title: 台帳が有効化された単一データベースを作成する
description: Azure portal を使用して、Azure SQL Database で台帳が有効化された単一データベースを作成します。
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: quickstart
author: rothja
ms.author: jroth
ms.reviewer: vanto
ms.date: 09/09/2021
ms.openlocfilehash: 8538e725583aea6fe765a7afd6e0ac58f176b443
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063743"
---
# <a name="quickstart-create-a-database-in-azure-sql-database-with-ledger-enabled"></a>クイックスタート: Azure SQL Database で台帳が有効化されたデータベースを作成する

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> 現在、Azure SQL Database 台帳はパブリック プレビュー段階にあります。

このクイックスタートでは、Azure portal を使用して、Azure SQL Database で[台帳データベース](ledger-overview.md#ledger-database)を作成し、さらに [Azure Blob Storage を使用した自動ダイジェスト ストレージ](ledger-digest-management-and-database-verification.md#automatic-generation-and-storage-of-database-digests)を構成します。 台帳の詳細については、[Azure SQL Database 台帳](ledger-overview.md)に関する記事を参照してください。

## <a name="prerequisite"></a>前提条件

アクティブな Azure サブスクリプションが必要です。 アカウントがない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="create-a-ledger-database-and-configure-digest-storage"></a>台帳データベースを作成し、ダイジェスト ストレージを構成する

[サーバーレス コンピューティング レベル](serverless-tier-overview.md)で単一の台帳データベースを作成し、Azure Storage アカウントへの台帳ダイジェストのアップロードを構成します。

### <a name="use-the-azure-portal"></a>Azure portal を使用する

Azure portal で単一データベースを作成するために、このクイックスタートは、Azure SQL のページから開始します。

1. [[Select SQL Deployment option]\(SQL デプロイ オプションの選択\)](https://portal.azure.com/#create/Microsoft.AzureSQL) ページを参照します。

1. **[SQL データベース]** で、 **[リソースの種類]** を **[単一データベース]** に設定し、 **[作成]** を選択します。

   ![Azure SQL への追加を示すスクリーンショット。](./media/single-database-create-quickstart/select-deployment.png)

1. **[SQL データベースの作成]** フォームの **[基本]** タブにある **[プロジェクトの詳細]** で、使用する Azure サブスクリプションを選択します。

1. **[リソース グループ]** で、 **[新規作成]** を選択し、「**myResourceGroup**」と入力して、 **[OK]** を選択します。

1. **[データベース名]** に「**demo**」と入力します。

1. **[サーバー]** の **[新規作成]** を選択します。 **[新しいサーバー]** フォームに次の値を入力します。
   - **[サーバー名]** : 「**mysqlserver**」と入力し、一意にするためにいくつかの文字を追加します。 サーバー名は、サブスクリプション内で一意ではなく、Azure のすべてのサーバーに対してグローバルに一意である必要があるため、正確なサーバー名をここに示すことはできません。 **mysqlserver12345** のように入力してから、利用可能かどうかをポータルで確認できます。
   - **サーバー管理者ログイン**:「**azureuser**」と入力します。
   - **パスワード**: 要件を満たすパスワードを入力します。 **[パスワードの確認入力]** ボックスにもう一度入力します。
   - **[場所]** :ドロップダウン リストから場所を選択します。
   - **[Allow Azure services to access this server]\(このサーバーへの Azure サービスのアクセスを許可する\)** : ダイジェスト ストレージへのアクセスを有効にするには、このオプションを選択します。
   
   **[OK]** を選択します。
   
1. **[SQL エラスティック プールを使用しますか?]** を **[いいえ]** に設定したままにします。

1. **[コンピューティングとストレージ]** で、 **[データベースの構成]** を選択します。

1. このクイックスタートではサーバーレス データベースを使用しているので、 **[サーバーレス]** を選択し、 **[適用]** を選択します。 

      ![サーバーレス データベースの構成を示すスクリーンショット。](./media/single-database-create-quickstart/configure-database.png)

1. **[ネットワーク]** タブの **[接続方法]** で、 **[パブリック エンドポイント]** を選択します。
1. **[ファイアウォール規則]** で、 **[現在のクライアント IP アドレスを追加する]** を **[はい]** に設定します。 **[Azure サービスおよびリソースにこのサーバー グループへのアクセスを許可する]** を **[いいえ]** に設定したままにします。
1. ページの下部で **[次へ: セキュリティ]** を選択します。

   :::image type="content" source="media/ledger/ledger-create-database-networking-tab.png" alt-text="Azure portal の [SQL データベースの作成] 画面の [ネットワーク] タブを示すスクリーンショット。":::

1. **[セキュリティ]** タブの **[台帳]** セクションで、 **[台帳の構成]** オプションを選択します。

    :::image type="content" source="media/ledger/ledger-configure-ledger-security-tab.png" alt-text="Azure portal の [セキュリティ] タブでの台帳の構成を示すスクリーンショット。":::

1. **[台帳の構成]** ペインの **[台帳]** セクションで、 **[Enable for all future tables in this database]\(このデータベースの将来のテーブルすべてで有効にする\)** チェックボックスを選択します。 この設定により、以降、データベース内のすべてのテーブルが台帳テーブルになります。 このため、データベース内のすべてのデータには、改ざんの証拠が示されるようになります。 既定では、[CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) で `LEDGER = ON` を指定しない場合でも、新しいテーブルは更新可能な台帳テーブルとして作成されます。 このオプションをオフのままにすることもできます。 この場合、Transact-SQL を使用して新しいテーブルを作成する際に、テーブルごとに台帳機能を有効にする必要があります。

1. **[Digest Storage]\(ダイジェスト ストレージ\)** セクションでは、 **[Enable automatic digest storage]\(自動ダイジェスト ストレージを有効にする\)** が自動的に選択されます。 これで、ダイジェストが格納される新しい Azure Storage アカウントとコンテナーが作成されます。

1. **[適用]** を選択します。

    :::image type="content" source="media/ledger/ledger-configure-ledger-pane.png" alt-text="Azure portal の [Configure ledger (preview)]\(台帳の構成 (プレビュー)\) ペインを示すスクリーンショット。":::

1. ページ下部にある **[確認と作成]** を選択します。

    :::image type="content" source="media/ledger/ledger-review-security-tab.png" alt-text="Azure portal の [セキュリティ] タブでの台帳データベースの確認と作成を示すスクリーンショット。":::

1. **[確認と作成]** ページで、確認後、 **[作成]** を選択します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次の手順のために、リソース グループ、サーバー、1 つデータベースを保持します。 データベースの台帳機能をさまざまな方法で使用する方法について学習します。

これらのリソースの使用が終わったら、作成したリソース グループを削除します。 このアクションにより、その中のサーバーと 1 つのデータベースも削除されます。

### <a name="use-the-azure-portal"></a>Azure portal を使用する

Azure portal を使用して **myResourceGroup** とそのすべてのリソースを削除するには、次の手順に従います。

1. ポータルで、 **[リソース グループ]** を検索して選択します。 一覧から **[myResourceGroup]** を選択します
1. [リソース グループ] ページで、 **[リソース グループの削除]** を選択します。
1. **[リソース グループ名を入力してください]** に「**myResourceGroup**」を入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

さまざまなツールと言語を使用してデータベースを接続してクエリを実行します。

- [更新可能な台帳テーブルを作成して使用する](ledger-how-to-updatable-ledger-tables.md)
- [追加専用のテーブルを作成して使用する](ledger-how-to-append-only-ledger-tables.md) 

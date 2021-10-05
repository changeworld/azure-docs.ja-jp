---
title: Azure Cosmos (SQL API) データベースをスキャンする方法
description: このハウツーガイドでは、Azure Cosmos Database (SQL API) をスキャンする方法の詳細について説明します。
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: 14799d5a3ae5878461775e8f581fdda44c140d30
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209967"
---
# <a name="register-and-scan-azure-cosmos-database-sql-api"></a>Azure Cosmos Database (SQL API) の登録とスキャン

この記事では、Azure Purview に Azure Cosmos Database (SQL API) アカウントを登録し、スキャンを設定する方法について説明します。

## <a name="supported-capabilities"></a>サポートされる機能

Azure Cosmos Database (SQL API) では、フル スキャンと増分スキャンがサポートされ、メタデータとスキーマがキャプチャされます。 また、スキャンでは、システムおよびカスタムの分類規則に基づいてデータが自動的に分類されます。

## <a name="prerequisites"></a>前提条件

- データ ソースを登録する前に、Azure Purview アカウントを作成します。 Purview アカウントの作成の詳細については、[クイック スタート: Azure Purview アカウントの作成](create-catalog-portal.md)に関するページを参照してください。
- Azure Purview データ ソース管理者である必要があります

## <a name="setting-up-authentication-for-a-scan"></a>スキャンでの認証の設定

Azure Cosmos Database (SQL API) の認証を設定する方法は 1 つだけです。

- アカウント キー
 
### <a name="account-key"></a>アカウント キー

選択した認証方法が **アカウント キー** の場合は、アクセス キーを取得して、キー コンテナーに格納する必要があります。

1. Azure Portal で Cosmos DB アカウントに移動します 
1. **[設定]**  >  **[キー]** を選択します 
1. "*読み取り/書き込みキー*" または "*読み取り専用キー*" からプライマリまたはセカンダリ キーをコピーし、次の手順のためにどこかに保存します。
1. お使いのキー コンテナーに移動する
1. **[設定] > [シークレット]** の順に選択します。
1. **[+ 生成/インポート]** を選択し、Azure Cosmos DB アカウントの "*キー*" として、 **[名前]** と **[値]** を入力します。
1. **[作成]** を選択して完了します。
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、キーを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します

## <a name="register-an-azure-cosmos-database-sql-api-account"></a>Azure Cosmos Database (SQL API) アカウントを登録する

新しい Azure Cosmos Database (SQL API) アカウントをデータ カタログに登録するには、次の手順を実行します。

1. Purview アカウントに移動します
1. 左側のナビゲーションで **[Data Map]** を選択します。
1. **[登録]** を選択します
1. **[ソースの登録]** で、 **[Azure Cosmos DB (SQL API)]** を選択します
1. **[続行]** を選択します

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="新しいデータ ソースの登録" border="true":::

**[ソースの登録 (Azure Cosmos DB (SQL API))]** 画面で、次の手順を実行します。

1. データ ソースがカタログに表示される際の **[名前]** を入力します。
2. お使いの Azure サブスクリプションを選択して、Azure Cosmos DB をフィルター処理します。
3. 該当する Cosmos DB アカウント名を選択します。
4. コレクションを選択するか、新しいものを作成します (省略可能)。
5. **[登録]** を選択してデータ ソースを登録します。

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="ソースの登録のオプション" border="true":::


## <a name="creating-and-running-a-scan"></a>スキャンを作成し、実行する

新しいスキャンを作成して実行するには、次の操作を行います。

1. [Purview Studio](https://web.purview.azure.com/resource/) の左側のペインで **[Data Map]** タブを選択します。

1. 登録した Azure Cosmos DB データ ソースを選択します。

1. **[新しいスキャン]** を選択します。

1. 対象のデータ ソースに接続するための資格情報を選択します。 

   :::image type="content" source="media/register-scan-azure-cosmos-database/set-up-scan-cosmos.png" alt-text="スキャンを設定する":::

1. リストから適切な項目を選択することによって、特定のデータベースに対するスキャンの範囲を指定することができます。

   :::image type="content" source="media/register-scan-azure-cosmos-database/cosmos-database-scope-your-scan.png" alt-text="スキャンの範囲を指定する":::

1. 次に、スキャン ルール セットを選択します。 システムの既定のものを選択するか、既存のカスタム ルール セットを使用するか、新しいルール セットをインラインで作成することができます。

   :::image type="content" source="media/register-scan-azure-cosmos-database/select-scan-rule-set.png" alt-text="スキャン ルール セット":::

1. スキャン トリガーを選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

   :::image type="content" source="media/register-scan-azure-cosmos-database/trigger-scan.png" alt-text="trigger":::

1. スキャンを確認し、 **[保存および実行]** を選択します。

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)

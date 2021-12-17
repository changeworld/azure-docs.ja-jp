---
title: Metrics Advisor サービスの暗号化
titleSuffix: Azure Cognitive Services
description: Metrics Advisor サービスによる保存データの暗号化。
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: mbullwin
ms.openlocfilehash: 03f6a30bbe857b59c3fa8a6fa668bdac7cb681b1
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342308"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>Metrics Advisor サービスによる保存データの暗号化

Metrics Advisor サービスの場合、データがクラウドに永続化されるときに、自動的に暗号化されます。 Metrics Advisor サービスの暗号化によってデータは保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。

[!INCLUDE [cognitive-services-about-encryption](../../cognitive-services/includes/cognitive-services-about-encryption.md)]

Metrics Advisor により、BYOS (独自ストレージの持ち込み) を使用した CMK と二重暗号化がサポートされます。 

## <a name="steps-to-create-a-metrics-advisor-with-byos"></a>BYOS を使用して Metrics Advisor を作成する手順

### <a name="step1-create-an-azure-database-for-postgresql-and-set-admin"></a>手順 1. Azure Database for PostgreSQL を作成して管理者を設定する

- Azure Database for PostgreSQL の作成

    Azure portal にログインし、Azure Database for PostgreSQL のリソースを作成します。 次の点に注意してください。

    1. **[単一サーバー]** デプロイ オプションを選択します。 
    2. [Datasource] を選択する場合は、 **[なし]** として指定してください。
    3. [場所] については、Metrics Advisor リソースと **同じ場所** に作成してください。
    4. [バージョン] は **11** に設定します。 
    5. [コンピューティングとストレージ] は、少なくとも **32 仮想コア** を含む "メモリ最適化" SKU を選択する必要があります。
    
    ![Azure Database for PostgreSQL の作成](media/cmk-create.png)

- 新しく作成された PG に Active Directory 管理者を設定します

    Azure Database for PostgreSQL が正常に作成された後、 新しく作成された Azure PG リソースのリソース ページに移動します。 [Active Directory 管理者] タブを選択し、自分を管理者として設定します。


### <a name="step2-create-a-metrics-advisor-resource-and-enable-managed-identity"></a>手順 2. Metrics Advisor リソースを作成し、マネージド ID を有効にする

- Azure portal で Metrics Advisor リソースを作成します

    もう一度 Azure portal に移動し、'Metrics Advisor' を検索します。 Metrics Advisor を作成する場合、次のことに注意してください。

    1. Azure Database for PostgreSQL を作成したときと **同じ 'リージョン'** を選択します。 
    2. [Bring your own storage] を **[はい]** に設定し、ドロップダウン リストから作成した [Azure Database for PostgreSQL] を選択します。

- Metrics Advisor のマネージド ID を有効にします

    Metrics Advisor リソースを作成した後、[ID] を選択し、[状態] を **[オン]** に設定してマネージド ID を有効にします。

- マネージド ID のアプリケーション ID を取得します

    Azure Active Directory に移動し、[エンタープライズ アプリケーション] を選択します。 [アプリケーションの種類] を **[マネージド ID]** に変更し、Metrics Advisor リソース名をコピーして検索します。 その後、クエリ結果から "アプリケーション ID" を表示してコピーできます。

### <a name="step3-grant-metrics-advisor-access-permission-to-your-azure-database-for-postgresql"></a>手順 3. Azure Database for PostgreSQL に Metrics Advisor アクセス許可を付与する

- Azure Database for PostgreSQL のマネージド ID に **"所有者"** ロールを付与します

- ファイアウォール規則を設定します

    1. [Azure サービスへのアクセスを許可] を [はい] に設定します。 
    2. clientIP アドレスを追加して、Azure Database for PostgreSQL にログインします。

- リソースの種類 'https://ossrdbms-aad.database.windows.net ' により、アカウントのアクセス トークンを取得します。 アクセス トークンは、お使いのアカウントで Azure Database for PostgreSQL にログインするために必要なパスワードです。 `az` クライアントを使用した例を次に示します。

   ```
   az login
   az account get-access-token --resource https://ossrdbms-aad.database.windows.net
   ```

- トークンを取得した後、それを使用して Azure Database for PostgreSQL にログインします。 'servername' は、Azure Database for PostgreSQL の "概要" で表示される値に置き換えます。

   ```
   export PGPASSWORD=<access-token>
   psql -h <servername> -U <adminaccount@servername> -d postgres
   ```

- ログイン後、次のコマンドを実行して、Azure Database for PostgreSQL に Metrics Advisor アクセス許可を付与します。 'appid' は、手順 2 で取得した値に置き換える必要があります。

   ```
   SET aad_validate_oids_in_tenant = off;
   CREATE ROLE metricsadvisor WITH LOGIN PASSWORD '<appid>' IN ROLE azure_ad_user;
   ALTER ROLE metricsadvisor CREATEDB;
   GRANT azure_pg_admin TO metricsadvisor;
   ```

上記のすべての手順を完了することで、CMK がサポートされている Metrics Advisor リソースが正常に作成されました。 Metrics Advisor にアクセスできるようになるまで、数分待ちます。

## <a name="next-steps"></a>次のステップ

* [Metrics Advisor サービス カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)
* [Azure Key Vault の詳細を確認する](../../key-vault/general/overview.md)
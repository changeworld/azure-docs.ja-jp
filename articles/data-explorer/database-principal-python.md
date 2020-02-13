---
title: Python を使用して Azure Data Explorer 用のデータベース プリンシパルを追加する
description: この記事では、Python を使用して Azure Data Explorer 用にデータベース プリンシパルを追加する方法について説明します。
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 8b9c4f4d5427b326c273558db0bff808068b192a
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965280"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-python"></a>Python を使用して Azure Data Explorer 用のデータベース プリンシパルを追加する

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Azure Resource Manager テンプレート](database-principal-resource-manager.md)

Azure Data Explorer は、ログと利用統計情報データのための高速で拡張性に優れたデータ探索サービスです。 この記事では、Python を使用して Azure Data Explorer 用にデータベース プリンシパルを追加します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [クラスターとデータベースを作成します](create-cluster-database-python.md)

## <a name="install-python-package"></a>Python パッケージのインストール

Azure Data Explorer (Kusto) 向けの Python パッケージをインストールするには、Python をパスに設定した状態でコマンド プロンプトを開きます。 次のコマンドを実行します。

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>データベース プリンシパルの追加

次の例は、プログラムによってデータベース プリンシパルを追加する方法を示しています。

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabasePrincipalAssignment
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster and database that is created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.database_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, principal_assignment_name= principal_assignment_name, parameters=DatabasePrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**設定** | **推奨値** | **フィールドの説明**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | テナント ID。 ディレクトリ ID とも呼ばれます。|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | リソースの作成に使用するサブスクリプション ID。|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ご利用のテナント内のリソースにアクセスできるアプリケーションのクライアント ID。|
| client_secret | *xxxxxxxxxxxxxx* | ご利用のテナント内のリソースにアクセスできるアプリケーションのクライアント シークレット。 |
| resource_group_name | *testrg* | ご利用のクラスターを含むリソース グループの名前。|
| cluster_name | *mykustocluster* | ご利用のクラスターの名前。|
| database_name | *mykustodatabase* | データベースの名前。|
| principal_assignment_name | *databasePrincipalAssignment1* | データベース プリンシパル リソースの名前。|
| principal_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | プリンシパル ID。ユーザーの電子メール、アプリケーション ID、またはセキュリティ グループ名を指定できます。|
| ロール (role) | *管理者* | データベース プリンシパルのロール。'Admin'、'Ingestor'、'Monitor'、'User'、'UnrestrictedViewers'、'Viewer' を指定できます。|
| tenant_id_for_principal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | プリンシパルのテナント ID。|
| principal_type | *アプリ* | プリンシパルの種類。'User'、'App'、または 'Group' を指定できます。|

## <a name="next-steps"></a>次のステップ

* [Azure Data Explorer の Python ライブラリを使用してデータを取り込む](python-ingest-data.md)

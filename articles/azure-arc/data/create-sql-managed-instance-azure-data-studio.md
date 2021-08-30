---
title: Azure Data Studio を使用して Azure SQL Managed Instance を作成する
description: Azure Data Studio を使用して Azure SQL Managed Instance を作成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 15d4ba669f736fd1322c137a16d8b79f40cea6d5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743883"
---
# <a name="create-sql-managed-instance---azure-arc-using-azure-data-studio"></a>Azure Data Studio を使用して Azure SQL Managed Instance - Azure Arc を作成する

このドキュメントでは、Azure Data Studio を使用して Azure SQL Managed Instance - Azure Arc をインストールする手順について説明します

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

## <a name="create-azure-sql-managed-instance-on-azure-arc"></a>Azure Arc で Azure SQL Managed Instance を作成する

- Azure Data Studio を起動する
- [接続] タブで、左上にある 3 つのドットをクリックし、[新しい展開] を選択します
- デプロイ オプションで、 **[Azure SQL Managed Instance - Azure Arc]\(Azure SQL Managed Instance - Azure Arc\)** を選択します 
  > [!NOTE]
  > 適切な CLI が現在インストールされていない場合は、ここでインストールするように求められることがあります。
- プライバシーとライセンス条項に同意し、下部にある **[選択]** をクリックします

- [Deploy Azure SQL Managed Instance - Azure Arc]\(Azure SQL Managed Instance - Azure Arc のデプロイ\) ブレードで、次の情報を入力します。
  - SQL Server インスタンスの名前を指定します
  - SQL Server インスタンスのパスワードを入力して確認します
  - データに適したストレージ クラスを選択します
  - ログに適したストレージ クラスを選択します

- **[デプロイ]** ボタンをクリックします

- これにより、データ コントローラーで Azure SQL Managed Instance - Azure Arc の作成が開始されます。

- 作成が正常に完了するまでに数分かかる場合があります。

## <a name="connect-to-azure-sql-managed-instance---azure-arc-from-azure-data-studio"></a>Azure Data Studio から Azure SQL Managed Instance - Azure Arc に接続します

- 次のコマンドを使用して、プロビジョニングされたすべての Azure SQL Managed Instances を表示します。

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
```

出力は次のようになります。ServerEndpoint (ポート番号を含む) をここからコピーします。

```console

Name          Replicas    ServerEndpoint     State
------------  ----------  -----------------  -------
sqlinstance1  1/1         25.51.65.109:1433  Ready
```

- Azure Data Studio の **[接続]** タブで、 **[サーバー]** ビューの **[新しい接続]** をクリックします
- **[接続]** ブレードで、ServerEndpoint を [サーバー] テキストボックスに貼り付けます
- 認証の種類として **[SQL ログイン]** を選択します
- ユーザー名として「*sa*」と入力します
- `sa` のアカウントのパスワードを入力します
- 必要に応じて、接続先の特定のデータベース名を入力します
- 必要に応じて、[Add New Server Group]\(新しいサーバー グループの追加\) を選択します
- **[接続]** を選択して、Azure SQL Managed Instance - Azure Arc に接続します

## <a name="next-steps"></a>次の手順

[SQL インスタンスの監視](monitor-grafana-kibana.md)を試行する

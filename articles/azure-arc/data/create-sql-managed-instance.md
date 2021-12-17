---
title: Azure Arc で Azure SQL Managed Instance を作成する
description: Azure Arc で Azure SQL Managed Instance を作成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: a5aac5704463b28ea9bb124f9fb3120d7a1da52b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123102077"
---
# <a name="create-an-azure-sql-managed-instance-on-azure-arc"></a>Azure Arc で Azure SQL Managed Instance を作成する

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]


## <a name="create-an-azure-sql-managed-instance"></a>Azure SQL マネージド インスタンスの作成

SQL Managed Instance の作成コマンドに利用できるオプションを表示するには、次のコマンドを使用します。
```azurecli
az sql mi-arc create --help
```

SQL Managed Instance を作成するには、次のコマンドを使用します。

```azurecli
az sql mi-arc create -n <instanceName> --k8s-namespace <namespace> --use-k8s
```

例:

```azurecli
az sql mi-arc create -n sqldemo --k8s-namespace my-namespace --use-k8s
```
> [!NOTE]
>  名前は、13 文字未満の長さで入力し、[DNS 名前付け規則](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-label-names)に準拠している必要があります。
>
>  メモリの割り当てと仮想コアの割り当てを指定する場合は、この数式を使用してパフォーマンスが許容範囲にあることを確認します。1 つの仮想コアごとに、SQL Managed Instance ポッドが実行される Kubernetes ノードで使用可能な RAM の容量が少なくとも 4 GB 必要です。
>
>  SQL インスタンスの作成を自動化し、管理者パスワードを求める対話型プロンプトが表示されないようにするには、`az sql mi-arc create` コマンドを実行する前に、`AZDATA_USERNAME` および `AZDATA_PASSWORD` 環境変数を目的のユーザー名とパスワードに設定します。
> 
>  同じターミナル セッションで AZDATA_USERNAME と AZDATA_PASSWORD を使用してデータ コントローラーを作成した場合、その AZDATA_USERNAME と AZDATA_PASSWORD の値は SQL Managed Instance の作成にも使用されます。

> [!NOTE]
> 間接接続モードを使用している場合、Kubernetes で Azure SQL Managed Instance を作成しても、Azure にリソースが自動登録されません。 リソースを登録する手順については、次の記事を参照してください。 
> - [課金データを Azure にアップロードして Azure portal に表示する](view-billing-data-in-azure.md) 


## <a name="view-instance-on-azure-arc"></a>Azure Arc でインスタンスを表示する

インスタンスを表示するには、次のコマンドを使用します。

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
```

ここから外部 IP とポート番号をコピーし、Azure Data Studio や SQL Server Management Studio などの SQL Server/Azure SQL インスタンスに接続するためのお気に入りのツールを使用してそれに接続できます。

[!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

## <a name="next-steps"></a>次のステップ
- [Azure Arc 対応 SQL Managed Instance に接続する](connect-managed-instance.md)
- [インスタンスを Azure に登録し、インスタンスに関するメトリックとログをアップロードする](upload-metrics-and-logs-to-azure-monitor.md)
- [Azure Data Studio を使用して Azure SQL Managed Instance をデプロイする](create-sql-managed-instance-azure-data-studio.md)

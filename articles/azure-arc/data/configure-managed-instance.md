---
title: Azure Arc 対応 SQL Managed Instance を構成する
description: Azure Arc 対応 SQL Managed Instance を構成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: e84d5be7252f81c4e80d6070ada2151fcc3960f1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743886"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 対応 SQL Managed Instance を構成する

この記事では、Azure Arc 対応 SQL Managed Instance を構成する方法について説明します。


## <a name="configure-resources"></a>Configure resources

### <a name="configure-using-cli"></a>CLI を使用した構成

Azure Arc 対応 SQL Managed Instance の構成を CLI を使用して編集できます。 次のコマンドを実行して構成オプションを確認します。 

```azurecli
az sql mi-arc edit --help
```

次の例では、CPU コアとメモリの要求と制限を設定します。

```azurecli
az sql mi-arc edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

SQL Managed Instance に対して行われた変更を確認するには、次のコマンドを使用して、構成 yaml ファイルを表示できます。

```azurecli
az sql mi-arc show -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

## <a name="configure-server-options"></a>サーバー オプションの構成

作成後に、Azure Arc 対応 SQL Managed Instance のサーバー構成設定を構成できます。 この記事では、mssql エージェントの有効化/無効化などの設定を構成する方法、およびトラブルシューティングのシナリオで特定のトレース フラグを有効にする方法について説明します。

これらのいずれかの設定を変更するには、次の手順に従います。

1. ターゲット設定を含むカスタム `mssql-custom.conf` ファイルを作成します。 次の例では、SQL エージェントを有効にし、トレース フラグ 1204 を有効にします。

   ```
   [sqlagent]
   enabled=true
   
   [traceflag]
   traceflag0 = 1204
   ```

1. `mssql-custom.conf` ファイルを `master-0` ポッドの `mssql-miaa` コンテナー内の `/var/opt/mssql` にコピーします。 `<namespaceName>` を Arc 名前空間の名前に置換します。

   ```bash
   kubectl cp mssql-custom.conf master-0:/var/opt/mssql/mssql-custom.conf -c mssql-server -n <namespaceName>
   ```

1. SQL Server インスタンスを再起動します。  `<namespaceName>` を Arc 名前空間の名前に置換します。

   ```bash
   kubectl exec -it master-0  -c mssql-server -n <namespaceName> -- /bin/bash
   supervisorctl restart mssql-server
   exit
   ```


**既知の制限事項**
- 上記の手順では、Kubernetes クラスター管理者のアクセス許可が必要です

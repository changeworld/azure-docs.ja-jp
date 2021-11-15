---
title: Azure Arc 対応 SQL Managed Instance を構成する
description: Azure Arc 対応 SQL Managed Instance を構成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: d4dc8843804d48fb98fef7cd336e6b56f54f49ae
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554357"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 対応 SQL Managed Instance を構成する

この記事では、Azure Arc 対応 SQL Managed Instance を構成する方法について説明します。


## <a name="configure-resources-such-as-cores-memory"></a>コア、メモリなどのリソースを構成する


### <a name="configure-using-cli"></a>CLI を使用した構成

Azure Arc 対応 SQL Managed Instance の構成を CLI を使用して編集できます。 次のコマンドを実行して構成オプションを確認します。 

```azurecli
az sql mi-arc edit --help
```

次のコマンドを使用して、Azure Arc 対応 SQL Managed Instance に利用できるメモリとコアを更新できます。

```azurecli
az sql mi-arc edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

次の例では、CPU コアとメモリの要求と制限を設定します。

```azurecli
az sql mi-arc edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n sqlinstance1 --k8s-namespace arc --use-k8s
```

Azure Arc 対応 SQL Managed Instance に対して行われた変更を確認するには、次のコマンドを使用して、構成 yaml ファイルを表示できます。

```azurecli
az sql mi-arc show -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

## <a name="configure-server-options"></a>サーバー オプションの構成

作成後に、Azure Arc 対応 SQL Managed Instance のサーバー構成設定を構成できます。 この記事では、mssql エージェントの有効化/無効化などの設定を構成する方法、およびトラブルシューティングのシナリオで特定のトレース フラグを有効にする方法について説明します。


### <a name="enable-sql-server-agent"></a>SQL Server エージェントを有効にする

既定では、SQL Server エージェントは無効になっています。 次のコマンドを実行して有効にできます。

```azurecli
az sql mi-arc edit -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s --agent-enabled true
```
例
```azurecli
az sql mi-arc edit -n sqlinstance1 --k8s-namespace arc --use-k8s --agent-enabled true
```

### <a name="enable-trace-flags"></a>トレース フラグを有効にする

トレース フラグは次のように有効にできます。
```azurecli
az sql mi-arc edit -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s --trace-flags "3614,1234" 
```


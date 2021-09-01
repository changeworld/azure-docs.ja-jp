---
title: Azure Arc で PostgreSQL Hyperscale サーバー グループの Postgres エンジン サーバー パラメーターを構成する
titleSuffix: Azure Arc-enabled data services
description: Azure Arc で PostgreSQL Hyperscale サーバー グループの Postgres エンジン サーバー パラメーターを構成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: e634bcc7d07cfba4016c8f2db323e78e9beda92a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737189"
---
# <a name="set-the-database-engine-settings-for-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale のデータベース エンジン設定を設定する

このドキュメントでは、PostgreSQL Hyperscale サーバー グループのデータベース エンジン設定をカスタム (既定以外の) 値に設定する手順について説明します。 設定できるデータベース エンジン パラメーターとその既定値について詳しくは、[こちら](https://www.postgresql.org/docs/current/runtime-config.html)の PostgreSQL に関するドキュメントをご覧ください。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!NOTE]
> プレビューでは、次のパラメーターの設定はサポートされていません。 
>
> - `archive_command`
> - `archive_timeout`
> - `log_directory`
> - `log_file_mode`
> - `log_filename`
> - `restore_command`
> - `shared_preload_libraries`
> - `synchronous_commit`
> - `ssl`
> - `wal_level`

## <a name="syntax"></a>構文

データベース エンジン設定を構成するためのコマンドの一般的な形式は次のとおりです。

```azurecli
az postgres arc-server edit -n <server group name>, [{--engine-settings, -e}] [{--replace-settings , --re}] {'<parameter name>=<parameter value>, ...'} --k8s-namespace <namespace> --use-k8s
```

## <a name="show-current-custom-values"></a>現在のカスタム値を表示する

### <a name="with-azure-data-cli-azdata-command"></a>[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] コマンドを使用する

```azurecli
az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
```

次に例を示します。

```azurecli
az postgres arc-server show -n postgres01 --k8s-namespace <namespace> --use-k8s 
```

このコマンドでは、設定したパラメーターを確認できるサーバー グループの仕様が返されます。 engine\settings セクションがない場合は、すべてのパラメーターが既定値で実行されていることになります。

```console
 "
...
engine": {
      "settings": {
        "default": {
          "autovacuum_vacuum_threshold": "65"
        }
      }
    },
...
```

### <a name="with-kubectl-command"></a>kubectl コマンドを使用する場合

以下の手順に従ってください。

1. サーバー グループのカスタム リソース定義の種類を取得する

   次を実行します。

   ```azurecli
   az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
   ```

   次に例を示します。

   ```azurecli
   az postgres arc-server show -n postgres01 --k8s-namespace <namespace> --use-k8s
   ```

   このコマンドでは、設定したパラメーターを確認できるサーバー グループの仕様が返されます。 engine\settings セクションがない場合は、すべてのパラメーターが既定値で実行されていることになります。

   ```output
   > {
     >"apiVersion": "arcdata.microsoft.com/v1alpha1",
     >"**kind**": "**postgresql-12**",
     >"metadata": {
       >"creationTimestamp": "2020-08-25T14:32:23Z",
       >"generation": 1,
       >"name": "postgres01",
       >"namespace": "arc",  
   ```

   出力結果で、フィールド `kind` を探し、値 (`postgresql-12` など) を控えておきます。

2. サーバー グループに対応する Kubernetes カスタム リソースを記述する 

   コマンドの一般的な形式は次のとおりです。

   ```console
   kubectl describe <kind of the custom resource> <server group name> -n <namespace name>
   ```

   次に例を示します。

   ```console
   kubectl describe postgresql-12 postgres01
   ```

   エンジン設定にカスタム値が設定されている場合は、それらが返されます。 次に例を示します。

   ```output
   Engine:
   ...
    Settings:
      Default:
        autovacuum_vacuum_threshold:  65
   ```

   どのエンジン設定にもカスタム値を設定していない場合、`resultset` の Engine Settings セクションは次のように空になります。

   ```output
   Engine:
   ...
       Settings:
         Default:
   ```

## <a name="set-custom-values-for-engine-settings"></a>エンジン設定にカスタム値を設定する

以下のコマンドでは、PostgreSQL Hyperscale のコーディネーター ノードとワーカー ノードのパラメーターが同じ値に設定されます。 サーバー グループのロールごとにパラメーターを設定することはまだできません。 つまり、指定のパラメーターを、コーディネーター ノードで特定の値に構成し、ワーカーノードで別の値に構成することはまだできません。

### <a name="set-a-single-parameter"></a>1 つのパラメーターを設定する

```azurecli
az postgres arc-server edit -n <server group name> --engine-settings  <parameter name>=<parameter value> --k8s-namespace <namespace> --use-k8s
```

次に例を示します。

```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  shared_buffers=8MB --k8s-namespace <namespace> --use-k8s
```

### <a name="set-multiple-parameters-with-a-single-command"></a>1 つのコマンドで複数のパラメーターを設定する

```azurecli
az postgres arc-server edit -n <server group name> --engine-settings  '<parameter name>=<parameter value>, <parameter name>=<parameter value>, --k8s-namespace <namespace> --use-k8s...'
```

次に例を示します。

```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'shared_buffers=8MB, max_connections=50' --k8s-namespace <namespace> --use-k8s
```

### <a name="reset-a-parameter-to-its-default-value"></a>パラメーターを既定値にリセットする

パラメーターを既定値にリセットするには、値を指定せずにそれを設定します。 

次に例を示します。

```azurecli
az postgres arc-server edit -n postgres01 --k8s-namespace <namespace> --use-k8s --engine-settings  shared_buffers=
```

### <a name="reset-all-parameters-to-their-default-values"></a>すべてのパラメーターを既定値にリセットする

```azurecli
az postgres arc-server edit -n <server group name> --engine-settings  '' -re --k8s-namespace <namespace> --use-k8s
```

次に例を示します。

```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  '' -re --k8s-namespace <namespace> --use-k8s
```

## <a name="special-considerations"></a>特別な考慮事項

### <a name="set-a-parameter-which-value-contains-a-comma-space-or-special-character"></a>値にコンマ、空白、または特殊文字が含まれるパラメーターを設定する

```azurecli
az postgres arc-server edit -n <server group name> --engine-settings  '<parameter name>="<parameter value>"' --k8s-namespace <namespace> --use-k8s
```

次に例を示します。

```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'custom_variable_classes = "plpgsql,plperl"' --k8s-namespace <namespace> --use-k8s
```

### <a name="pass-an-environment-variable-in-a-parameter-value"></a>パラメーター値で環境変数を渡す

環境変数は、設定前に解決されないように "''" 内にラップする必要があります。

次に例を示します。 

```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'search_path = "$user"' --k8s-namespace <namespace> --use-k8s
```

## <a name="next-steps"></a>次のステップ
- サーバー グループの[スケール アウト (ワーカー ノードの追加)](scale-out-in-postgresql-hyperscale-server-group.md) について確認する
- サーバー グループの[スケールアップまたはスケール ダウン (メモリ/仮想コアの増減)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) について確認する

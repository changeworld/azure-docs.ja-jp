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
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 609ed203c794447f4fd0e0cd1a5267186a8eb67e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301743"
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

## <a name="show-current-custom-values-if-they-have-been-set"></a>現在のカスタム値が設定されている場合は表示する

**Az CLI コマンドの場合は、以下のようになります:**

```azurecli
az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
```

次に例を示します。

```azurecli
az postgres arc-server show -n postgres01 --k8s-namespace arc --use-k8s 
```


**または、kubectl コマンドの場合は、以下のようになります:**
```console
   kubectl describe postgresql <server group name> -n <namespace name>
   ```

   次に例を示します。

   ```console
   kubectl describe postgresql postgres01 -n arc
```

どちらのコマンドでも、設定したパラメーターを確認できるサーバー グループの仕様が返されます。 engine\settings セクションがない場合は、すべてのパラメーターが既定値で実行されていることになります。

:::row:::
    :::column:::
        Postgres エンジンの設定にカスタム値が設定されていない場合の出力例。 仕様には engine\settings セクションが表示されません。
    :::column-end:::
    :::column:::
        ```console
          ...
          "spec": {
            "dev": false,
            "engine": {
              "extensions": [
                {
                  "name": "citus"
                }
              ],
              "version": 12
            },
            "scale": {
              "replicas": 1,
              "syncReplicas": "0",
          "workers": 4
            },
            ...
        ```
        :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        Example of an output when custom values have been set for some of Postgres engine setting. The specs do show a section engine\settings.
    :::column-end:::
    :::column:::
        ```console
             ...
                Spec:
                  Dev:  false
                  Engine:
                    Extensions:
                      Name:  citus
                    Settings:
                      Default:
                        max_connections:  51
                      Roles:
                        Coordinator:
                          max_connections:  53
                        Worker:
                          max_connections:  52
                    Version:                12
                  Scale:
                    Replicas:       1
                    Sync Replicas:  0
                    Workers:        4
            ...
            ```
    :::column-end:::
:::row-end:::


The default value is, refer to the PostgreSQL documentation [here](https://www.postgresql.org/docs/current/runtime-config.html).



## Set custom values for engine settings

### Set a single parameter

**On both coordinator and worker roles:**

General syntax of the command:
```azurecli
az postgres arc-server edit -n <servergroup name> --engine-settings  '<ParameterName>=<CustomParameterValue>' --k8s-namespace <namespace> --use-k8s
```

次に例を示します。
```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'max_connections=51' --k8s-namespace arc --use-k8s
```

**worker ロールの場合のみ:**

コマンドの一般的な構文は次のとおりです。
```azurecli
az postgres arc-server edit -n <servergroup name> --worker-settings '<ParameterName>=<CustomParameterValue>' --k8s-namespace <namespace> --use-k8s
```

次に例を示します。
```azurecli
az postgres arc-server edit -n postgres01 --worker-settings 'max_connections=52' --k8s-namespace arc --use-k8s
```

**coordinator ロールの場合のみ:**

コマンドの一般的な構文は次のとおりです。
```azurecli
az postgres arc-server edit -n <servergroup name> --coordinator-settings '<ParameterName>=<CustomParameterValue>' --k8s-namespace <namespace> --use-k8s
```

次に例を示します。
```azurecli
az postgres arc-server edit -n postgres01 --coordinator-settings 'max_connections=53' --k8s-namespace arc --use-k8s
```



### <a name="set-multiple-parameters-with-a-single-command"></a>1 つのコマンドで複数のパラメーターを設定する

**coordinator および worker ロールの両方:**  
 
コマンドの一般的な構文は次のとおりです。
```azurecli
az postgres arc-server edit -n <servergroup name> --engine-settings  '<ParameterName1>=<CustomParameterValue1>, ..., <ParameterNameN>=<CustomParameterValueN>' --k8s-namespace <namespace> --use-k8s
```

次に例を示します。
```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'shared_buffers=8MB, max_connections=60' --k8s-namespace arc --use-k8s
```

**worker ロールの場合のみ:**

コマンドの一般的な構文は次のとおりです。
```azurecli
az postgres arc-server edit -n <servergroup name> --worker-settings '<ParameterName1>=<CustomParameterValue1>, ..., <ParameterNameN>=<CustomParameterValueN>' --k8s-namespace <namespace> --use-k8s
```

次に例を示します。
```azurecli
az postgres arc-server edit -n postgres01 --worker-settings 'shared_buffers=8MB, max_connections=60' --k8s-namespace arc --use-k8s
```

**coordinator ロールの場合のみ:**

コマンドの一般的な構文は次のとおりです。
```azurecli
az postgres arc-server edit -n <servergroup name> --coordinator-settings '<ParameterName1>=<CustomParameterValue1>, ..., <ParameterNameN>=<CustomParameterValueN>' --k8s-namespace <namespace> --use-k8s
```

次に例を示します。
```azurecli
az postgres arc-server edit -n postgres01 --coordinator-settings 'shared_buffers=8MB, max_connections=60' --k8s-namespace arc --use-k8s
```

### <a name="reset-one-parameter-to-its-default-value"></a>1 つのパラメーターを既定値にリセットします

**coordinator および worker ロールの両方:**

コマンドの一般的な構文は次のとおりです。
```azurecli
az postgres arc-server edit -n <servergroup name> --engine-settings '<ParameterName>='  --coordinator-settings '<ParameterName>=' --worker-settings '<ParameterName>=' --k8s-namespace <namespace> --use-k8s
```
次に例を示します。
```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'shared_buffers='  --coordinator-settings 'shared_buffers=' --worker-settings 'shared_buffers=' --k8s-namespace arc --use-k8s
```

**coordinator ロールの場合のみ:**

コマンドの一般的な構文は次のとおりです。
```azurecli
az postgres arc-server edit -n <servergroup name> --coordinator-settings '<ParameterName>=' --k8s-namespace <namespace> --use-k8s
```
次に例を示します。
```azurecli
az postgres arc-server edit -n postgres01 --coordinator-settings 'shared_buffers=' --k8s-namespace arc --use-k8s
```

**worker ロールの場合のみ:**

コマンドの一般的な構文は次のとおりです。
```azurecli
az postgres arc-server edit -n <servergroup name> --worker-settings '<ParameterName>=' --k8s-namespace <namespace> --use-k8s
```
次に例を示します。
```azurecli
az postgres arc-server edit -n postgres01 --worker-settings 'shared_buffers=' --k8s-namespace arc --use-k8s
````

### <a name="reset-all-parameters-to-their-default-values"></a>すべてのパラメーターを既定値にリセットする

**coordinator および worker ロールの両方:**

コマンドの一般的な構文は次のとおりです。
```azurecli
az postgres arc-server edit -n <servergroup name> --engine-settings  `'`' --worker-settings `'`' --coordinator-settings `'`' --replace-settings --k8s-namespace <namespace> --use-k8s
```

次に例を示します。
```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  `'`' --worker-settings `'`' --coordinator-settings `'`'  --replace-settings --k8s-namespace arc --use-k8s
```

**coordinator ロールの場合のみ:**

コマンドの一般的な構文は次のとおりです。
```azurecli
az postgres arc-server edit -n <servergroup name> --coordinator-settings `'`'  --replace-settings --k8s-namespace <namespace> --use-k8s

```
次に例を示します。
```azurecli
az postgres arc-server edit -n postgres01 --coordinator-settings `'`'  --replace-settings --k8s-namespace arc --use-k8s
```

**worker ロールの場合のみ:**

コマンドの一般的な構文は次のとおりです。
```azurecli
az postgres arc-server edit -n <servergroup name> --worker-settings `'`'  --replace-settings --k8s-namespace <namespace> --use-k8s
```
次に例を示します。
```azurecli
az postgres arc-server edit -n postgres01 --worker-settings `'`'  --replace-settings --k8s-namespace arc --use-k8s
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

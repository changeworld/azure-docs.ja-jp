---
title: Azure Arc で PostgreSQL Hyperscale サーバー グループの Postgres エンジン サーバー パラメーターを構成する
titleSuffix: Azure Arc enabled data services
description: Azure Arc で PostgreSQL Hyperscale サーバー グループの Postgres エンジン サーバー パラメーターを構成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4e8813647211e0adbfe43a45ae0d19dc12a4a165
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90931334"
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

```console
azdata arc postgres server edit -n <server group name>, [{--engine-settings, -e}] [{--replace-engine-settings, --re}] {'<parameter name>=<parameter value>, ...'}
```

## <a name="show-the-current-custom-values-of-the-parameters-settings"></a>パラメーター設定の現在のカスタム値を表示する

## <a name="with-azdata-cli-command"></a>azdata CLI コマンドを使用する場合

```console
azdata arc postgres server show -n <server group name>
```

次に例を示します。

```console
azdata arc postgres server show -n postgres01
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

## <a name="with-kubectl-command"></a>kubectl コマンドを使用する場合

以下の手順に従ってください。

### <a name="1-retrieve-the-kind-of-custom-resource-definition-for-your-server-group"></a>1.サーバー グループのカスタム リソース定義の種類を取得する

次を実行します。

```console
azdata arc postgres server show -n <server group name>
```

次に例を示します。

```console
azdata arc postgres server show -n postgres01
```

このコマンドでは、設定したパラメーターを確認できるサーバー グループの仕様が返されます。 engine\settings セクションがない場合は、すべてのパラメーターが既定値で実行されていることになります。

```
> {
  >"apiVersion": "arcdata.microsoft.com/v1alpha1",
  >"**kind**": "**postgresql-12**",
  >"metadata": {
    >"creationTimestamp": "2020-08-25T14:32:23Z",
    >"generation": 1,
    >"name": "postgres01",
    >"namespace": "arc",
```

ここで、フィールド"kind" を探し、値 (`postgresql-12` など) を控えておきます。

### <a name="2-describe-the-kubernetes-custom-resource-corresponding-to-your-server-group"></a>2.サーバー グループに対応する Kubernetes カスタム リソースを記述する 

コマンドの一般的な形式は次のとおりです。

```console
kubectl describe <kind of the custom resource> <server group name> -n <namespace name>
```

次に例を示します。

```console
kubectl describe postgresql-12 postgres01
```

エンジン設定にカスタム値が設定されている場合は、それらが返されます。 次に例を示します。

```console
Engine:
...
    Settings:
      Default:
        autovacuum_vacuum_threshold:  65
```

どのエンジン設定にもカスタム値を設定していない場合、結果セットの Engine Settings セクションは次のように空になります。

```console
Engine:
...
    Settings:
      Default:
```

## <a name="set-custom-values-for-the-engine-settings"></a>エンジン設定にカスタム値を設定する

以下のコマンドでは、PostgreSQL Hyperscale のコーディネーター ノードとワーカー ノードのパラメーターが同じ値に設定されます。 サーバー グループのロールごとにパラメーターを設定することはまだできません。 つまり、指定のパラメーターを、コーディネーター ノードで特定の値に構成し、ワーカーノードで別の値に構成することはまだできません。

## <a name="set-a-single-parameter"></a>1 つのパラメーターを設定する

```console
azdata arc server edit -n <server group name> -e <parameter name>=<parameter value>
```

次に例を示します。

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=8MB
```

## <a name="set-multiple-parameters-with-a-single-command"></a>1 つのコマンドで複数のパラメーターを設定する

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>=<parameter value>, <parameter name>=<parameter value>,...'
```

次に例を示します。

```console
azdata arc postgres server edit -n postgres01 -e 'shared_buffers=8MB, max_connections=50'
```

## <a name="reset-a-parameter-to-its-default-value"></a>パラメーターを既定値にリセットする

パラメーターを既定値にリセットするには、値を指定せずにそれを設定します。 

次に例を示します。

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=
```

## <a name="reset-all-parameters-to-their-default-values"></a>すべてのパラメーターを既定値にリセットする

```console
azdata arc postgres server edit -n <server group name> -e '' -re
```

次に例を示します。

```console
azdata arc postgres server edit -n postgres01 -e '' -re
```

## <a name="special-considerations"></a>特別な考慮事項

### <a name="set-a-parameter-which-value-contains-a-comma-space-or-special-character"></a>値にコンマ、空白、または特殊文字が含まれるパラメーターを設定する

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>="<parameter value>"'
```

次に例を示します。

```console
azdata arc postgres server edit -n postgres01 -e 'custom_variable_classes = "plpgsql,plperl"'
```

### <a name="pass-an-environment-variable-in-a-parameter-value"></a>パラメーター値で環境変数を渡す

環境変数は、設定前に解決されないように "''" 内にラップする必要があります。

次に例を示します。 

```console
azdata arc postgres server edit -n postgres01 -e 'search_path = "$user"'
```



## <a name="next-steps"></a>次のステップ
- サーバー グループの[スケール アウト (ワーカー ノードの追加)](scale-out-postgresql-hyperscale-server-group.md) について確認する
- サーバー グループの[スケールアップまたはスケール ダウン (メモリ/仮想コアの増減)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) について確認する

---
title: Bicep 構成のモジュール設定
description: Bicep デプロイ内のモジュールの構成値をカスタマイズする方法について説明します。
ms.topic: conceptual
ms.date: 11/16/2021
ms.openlocfilehash: e130695532d470a154acc0b35bca864fcdbcba9d
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557109"
---
# <a name="add-module-settings-in-the-bicep-config-file"></a>Bicep 構成ファイルにモジュール設定を追加する

**bicepconfig.json** ファイルでは、モジュールのパスに別名を作成し、モジュール復元用の資格情報の優先順位を構成できます。

この記事では、[モジュール](modules.md)の操作に使用できる設定について説明します。

## <a name="aliases-for-modules"></a>モジュールの別名

モジュールにリンクするためのパスを簡略化するため、構成ファイル内に別名を作成します。 別名によって、モジュール レジストリ、またはテンプレート スペックを含むリソース グループのいずれかを参照します。

構成ファイルには、`moduleAliases` のプロパティがあります。 このプロパティには、定義するすべての別名が格納されます。 このプロパティでは、別名は、それがレジストリとテンプレート スペックのどちらを参照しているかに基づいて分割されます。

**Bicep レジストリ** の別名を作成するには、`br` プロパティを追加します。 **テンプレート スペック** の別名を追加するには、`ts` プロパティを使用します。

```json
{
  "moduleAliases": {
    "br": {
      <add-registry-aliases>
    },
    "ts": {
      <add-template-specs-aliases>
    }
  }
}
```

`br` プロパティ内に、必要な数の別名を追加します。 別名ごとに名前と次のプロパティを指定します。

- **registry** (必須): レジストリ ログイン サーバー名
- **modulePath** (省略可能): モジュールが格納されているレジストリ リポジトリ

`ts` プロパティ内に、必要な数の別名を追加します。 別名ごとに名前と次のプロパティを指定します。

- **subscription** (必須): テンプレート スペックをホストするサブスクリプション ID
- **resourceGroup** (必須): テンプレート スペックを含むリソース グループの名前

次の例は、モジュール レジストリの 2 つの別名と、テンプレート スペックを含むリソース グループの 1 つの別名を定義する構成ファイルを示しています。

```json
{
  "moduleAliases": {
    "br": {
      "ContosoRegistry": {
        "registry": "contosoregistry.azurecr.io"
      },
      "CoreModules": {
        "registry": "contosoregistry.azurecr.io",
        "modulePath": "bicep/modules/core"
      }
    },
    "ts": {
      "CoreSpecs": {
        "subscription": "00000000-0000-0000-0000-000000000000",
        "resourceGroup": "CoreSpecsRG"
      }
    }
  }
}
```

モジュール参照で別名を使用する場合は、次の形式を使用する必要があります。

```bicep
br/<alias>:<file>:<tag>
ts/<alias>:<file>:<tag>
```

ファイル自体ではなく、モジュールを含むフォルダーまたはリソース グループに別名を定義します。 ファイル名は、モジュールへの参照に含める必要があります。

**別名がない場合**、レジストリ内のモジュールにリンクするには完全なパスを使用します。

```bicep
module stgModule 'br:contosoregistry.azurecr.io/bicep/modules/core/storage:v1' = {
```

**別名を使用すると**、レジストリの別名を使用することでリンクを簡略化できます。

```bicep
module stgModule 'br/ContosoRegistry:bicep/modules/core/storage:v1' = {
```

または、レジストリとモジュールのパスを指定する別名を使用してリンクを簡略化できます。

```bicep
module stgModule  'br/CoreModules:storage:v1' = {
```

テンプレート スペックの場合は、以下を使用します。

```bicep
module stgModule  'ts/CoreSpecs:storage:v1' = {
```

## <a name="credentials-for-restoring-modules"></a>モジュールを復元するための資格情報

外部モジュールをローカル キャッシュに[復元](bicep-cli.md#restore)するには、レジストリにアクセスするための適切なアクセス許可がアカウントに必要です。 レジストリに対する認証のための資格情報に優先順位を構成できます。 Bicep の既定では、Azure CLI または Azure PowerShell で認証されたユーザーからの資格情報が使用されます。 資格情報の優先順位をカスタマイズするには、`cloud` および `credentialPrecedence` 要素を構成ファイルに追加します。

```json
{
    "cloud": {
      "credentialPrecedence": [
        "AzureCLI",
        "AzurePowerShell"
      ]
    }
}
```

使用可能な資格情報は次のとおりです。

* AzureCLI
* AzurePowerShell
* 環境
* ManagedIdentity
* VisualStudio
* VisualStudioCode

## <a name="next-steps"></a>次のステップ

* [Bicep 環境を構成する](bicep-config.md)
* [Bicep 構成にリンター設定を追加する](bicep-config-linter.md)
* [モジュール](modules.md)について学習する

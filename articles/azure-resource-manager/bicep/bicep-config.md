---
title: Bicep 構成ファイル
description: Bicep デプロイの構成値をカスタマイズする方法について説明します
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 7a057d353fd5b25ae122e7856f1ccb560d7fce56
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130071836"
---
# <a name="add-custom-settings-in-the-bicep-config-file"></a>Bicep 構成ファイルにカスタム設定を追加する

Bicep デプロイの構成値をカスタマイズするには、Bicep ファイルを格納するディレクトリに **bicepconfig.json** という名前のファイルを追加します。 このファイル内で、使用する構成値を指定します。

複数の bicepconfig.json ファイルを追加できます。 ディレクトリ階層内で最も近い構成ファイルが使用されます。

この記事では、構成ファイルで使用できるプロパティについて説明します。 ただし、これらのプロパティは、Bicep 拡張機能によって Visual Studio Code 用に提供される intellisense を通じて検出されます。

:::image type="content" source="./media/bicep-config/bicep-linter-configure-intellisense.png" alt-text="intellisense は bicepconfig.json の構成をサポートしています。":::

## <a name="aliases-for-modules"></a>モジュールの別名

モジュールにリンクするパスを簡略化するために、構成ファイル内で別名を作成できます。 別名を使用して、モジュール レジストリまたはテンプレート スペックを含むリソース グループを参照できます。 構成ファイルには、`moduleAliases` のプロパティがあります。 Bicep レジストリの別名を作成するには、`moduleAliases` プロパティの下に `br` プロパティを追加します。 テンプレート スペックの別名を追加するには、`ts` プロパティを使用します。

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

## <a name="customize-linter"></a>リンターのカスタマイズ

構成ファイルで、[Bicep リンター](linter.md)の設定をカスタマイズします。 リンターを有効または無効に設定し、ルール固有の値を指定することができます。また、ルールのレベルも設定できます。

次の例は、構成に使用できる規則を示しています。

```json
{
  "analyzers": {
    "core": {
      "enabled": true,
      "verbose": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning"
        },
        "no-unused-params": {
          "level": "error"
        },
        "no-unused-vars": {
          "level": "error"
        },
        "prefer-interpolation": {
          "level": "warning"
        },
        "secure-parameter-default": {
          "level": "error"
        },
        "simplify-interpolation": {
          "level": "warning"
        }
      }
    }
  }
}
```

次のプロパティです。

- **enabled**: リンターを有効にする場合は **true** を、リンターを無効にする場合は **false** を指定します。
- **verbose**: Visual Studio Code で使用される bicepconfig.json ファイルを表示するには、**true** を指定します。
- **rules**: ルール固有の値を入力します。 各ルールには、違反が検出された場合にリンターがどのように応答するかを決定するレベルがあります。

**level** に使用できる値は次のとおりです。

| **level**  | **ビルド時の動作** | **エディターの動作** |
|--|--|--|
| `Error` | 違反は、コマンドラインのビルド出力にエラーとして表示され、それによってビルドが失敗します。 | 問題のあるコードには赤色の波線で下線が引かれ、[問題] タブに表示されます。 |
| `Warning` | 違反は、コマンドラインのビルド出力に警告として表示されますが、ビルドは失敗しません。 | 問題のあるコードには黄色の波線で下線が引かれ、[問題] タブに表示されます。 |
| `Info` | 違反は、コマンドラインのビルド出力に表示されません。 | 問題のあるコードには青色の波線で下線が引かれ、[問題] タブに表示されます。 |
| `Off` | 完全に抑制されます。 | 完全に抑制されます。 |

ハードコーディングされた環境 URL に関する規則については、どの URL をチェックするかをカスタマイズできます。 既定では、次の設定が適用されます。

```json
{
  "analyzers": {
    "core": {
      "verbose": false,
      "enabled": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning",
          "disallowedhosts": [
            "management.core.windows.net",
            "gallery.azure.com",
            "management.core.windows.net",
            "management.azure.com",
            "database.windows.net",
            "core.windows.net",
            "login.microsoftonline.com",
            "graph.windows.net",
            "trafficmanager.net",
            "vault.azure.net",
            "datalake.azure.net",
            "azuredatalakestore.net",
            "azuredatalakeanalytics.net",
            "vault.azure.net",
            "api.loganalytics.io",
            "api.loganalytics.iov1",
            "asazure.windows.net",
            "region.asazure.windows.net",
            "api.loganalytics.iov1",
            "api.loganalytics.io",
            "asazure.windows.net",
            "region.asazure.windows.net",
            "batch.core.windows.net"
          ],
          "excludedhosts": [
            "schema.management.azure.com"
          ]
        }
      }
    }
  }
}
```



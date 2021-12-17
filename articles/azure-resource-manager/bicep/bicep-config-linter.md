---
title: Bicep 構成のリンター設定
description: Bicep リンターの構成値をカスタマイズする方法について説明します
ms.topic: conceptual
ms.date: 11/16/2021
ms.openlocfilehash: d48ea625d58b35ac18c39bfb3fb60eb4ec3d7edd
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557142"
---
# <a name="add-linter-settings-in-the-bicep-config-file"></a>Bicep 構成ファイルにリンター設定を追加する

**bicepconfig.json** ファイルで、[Bicep リンター](linter.md)の検証設定をカスタマイズできます。 リンターでは、ベスト プラクティスに関して Bicep ファイルを評価するときに、これらの設定が使用されます。

この記事では、Bicep リンターの操作に使用できる設定について説明します。

## <a name="customize-linter"></a>リンターのカスタマイズ

リンター設定は、`analyzers` 要素の下で使用できます。 リンターを有効または無効に設定し、ルール固有の値を指定することができます。また、ルールのレベルも設定できます。

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

## <a name="environment-urls"></a>環境 URL

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

## <a name="next-steps"></a>次のステップ

* [Bicep 環境を構成する](bicep-config.md)
* [Bicep 構成にモジュール設定を追加する](bicep-config-modules.md)
* [Bicep リンター](linter.md)について学習する

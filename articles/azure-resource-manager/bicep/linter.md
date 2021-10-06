---
title: Bicep リンターの使用方法
description: Bicep linter の使用方法について説明します。
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: cef701d9a9f64990c0afbe265c3355f9c1a850ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631174"
---
# <a name="use-bicep-linter"></a>Bicep リンターの使用方法

Bicep リンターを使用して、Bicep ファイルを分析できます。 これにより、Bicep ファイルをビルドまたはデプロイする前に、構文エラーとベスト プラクティス違反を見つけることができます。 ファイルのチェックに使用する作成のベスト プラクティスのセットをカスタマイズできます。 リンターを使用すると、開発時にガイダンスを提供することで、コーディング標準を簡単に適用できます。

## <a name="linter-requirements"></a>リンターの要件

リンターが Bicep CLI および VS Code 拡張機能に統合されている。 使用するには、バージョン 0.4 以降が必要です。

## <a name="customize-linter"></a>リンターのカスタマイズ

bicepconfig.json を使用して、リンターを有効または無効に設定し、ルール固有の値を指定することができます。また、ルールのレベルも設定できます。 次の例で、既定の bicepconfig.json を示します。

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

カスタマイズされた bicepconfig.json を、同じディレクトリー内のテンプレートと一緒に配置できます。 フォルダー ツリーで最も近い構成ファイルが使用されます。

次の json は、サンプル bicepconfig.jsです。

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

- **有効**: リンターを有効にする場合は **true** を入力し、リンターを無効にする場合は **false** を入力します。
- **verbose**: Visual Studio Code で使用される bicepconfig.json ファイルを表示するには、**true** を入力します。
- **ルール**: ルール固有の値を入力します。 各ルールには、少なくとも 1 つのプロパティとレベルがあります。 このプロパティは、Bicep ファイルでケースが検出されると、Bicep の動作を指示します。

ルールレベルには、次のような複数の値を使用できます。

| **level**  | **ビルド時の動作** | **エディターの動作** |
|--|--|--|
| `Error` | 違反は、コマンドラインのビルド出力にエラーとして表示され、ビルドが失敗します。 | 問題のあるコードには赤色の波線で下線が引かれ、[問題] タブに表示されます。 |
| `Warning` | 違反は、コマンドラインのビルド出力に警告として表示されますが、ビルドは失敗しません。 | 問題のあるコードには黄色の波線で下線が引かれ、[問題] タブに表示されます。 |
| `Info` | 違反は、コマンドラインのビルド出力に表示されません。 | 問題のあるコードには青色の波線で下線が引かれ、[問題] タブに表示されます。 |
| `Off` | 完全に抑制されます。 | 完全に抑制されます。 |

現在のリンター ルールのセットは最小限であり、[arm-ttk test cases](../templates/template-test-cases.md) から取得されます。 Visual Studio Code 拡張機能と Bicep CLI の両方が、既定で使用可能なすべてのルールを確認し、すべてのルールが警告レベルに設定されます。 ルールのレベルに基づいて、エラー、警告、または情報のメッセージがエディター内に表示されます。

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Visual Studio Code の Bicep 拡張機能には、Bicep 構成ファイルを編集するための intellisense が用意されています。

:::image type="content" source="./media/linter/bicep-linter-configure-intellisense.png" alt-text="intellisense は bicepconfig.json の構成をサポートしています。":::

## <a name="use-in-visual-studio-code"></a>Visual Studio Code での使用

リンターを使用するには、Bicep 拡張 0.4 以降をインストールします。  次のスクリーンショットは、動作中のリンターを示しています。

:::image type="content" source="./media/linter/bicep-linter-show-errors.png" alt-text="Visual Studio Code での Bicep リンターの使用状況。":::

以下のスクリーンショットに示されているように、 **[問題]** ペインには 4 つのエラーと、1 つの警告、1 つの情報メッセージがあります。  情報メッセージには、使用されている bicep 構成ファイルが表示されます。 構成ファイルで **verbose** を **true** に設定した場合、ここにはこの情報のみが表示されます。

問題のある領域のいずれかに、マウス カーソルを置きます。 リンターによって、エラーまたは警告に関する詳細が示されます。 領域を選択すると、青い電球も表示されます。

:::image type="content" source="./media/linter/bicep-linter-show-quickfix.png" alt-text="Visual Studio Code での Bicep リンターの使用状況- quickfix の表示。":::

青い電球または **Quick fix** リンクを選択し、ソリューションを表示します。

:::image type="content" source="./media/linter/bicep-linter-show-quickfix-solution.png" alt-text="Visual Studio Code での Bicep リンターの使用状況 - quickfix ソリューションの表示。":::

ソリューションを選択して、問題を自動的に修正します。

## <a name="use-in-bicep-cli"></a>Bicep CLI での使用

リンターを使用するには、Bicep CLI 0.4 以降をインストールします。  次のスクリーンショットは、動作中のリンターを示しています。 Bicep ファイルは、[Visual Studio Code での使用](#use-in-visual-studio-code)での使用法と同じです。

:::image type="content" source="./media/linter/bicep-linter-command-line.png" alt-text="コマンド ラインでの Bicep リンターの使用方法。":::

これらのチェックは、CI/CD パイプラインの一部として統合できます。 GitHub アクションを使用して、bicep ビルドを試みることができます。 エラーにより、パイプラインが失敗します。

## <a name="next-steps"></a>次のステップ

Visual Studio Code と Bicep 拡張機能の使用方法の詳細については、「[クイック スタート: Visual Studio Code を使用して Bicep ファイルを作成する](./quickstart-create-bicep-use-visual-studio-code.md)」を参照してください。

---
title: Bicep リンターの使用方法
description: Bicep linter の使用方法について説明します。
ms.topic: conceptual
ms.date: 10/12/2021
ms.openlocfilehash: 5edf5fbb9553233455e7b24dd482a18d3c7e1c42
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548104"
---
# <a name="use-bicep-linter"></a>Bicep リンターの使用方法

Bicep リンターは、Bicep ファイルに構文エラーとベスト プラクティス違反がないかチェックします。 リンターを使用すると、開発時のガイダンスが提供され、コーディング標準を適用できます。 ファイルのチェックに使用するベスト プラクティスをカスタマイズできます。

## <a name="linter-requirements"></a>リンターの要件

リンターは、Bicep CLI と、Visual Studio Code 用の Bicep 拡張機能に統合されています。 使用するには、バージョン **0.4 以降** が必要です。

## <a name="default-rules"></a>既定のルール

既定のリンター ルールのセットは最小限であり、[arm-ttk テスト ケース](../templates/template-test-cases.md)から取得されます。 拡張機能と Bicep CLI では、警告レベルに設定されている次のルールがチェックされます。

- [adminusername-should-not-be-literal](./linter-rule-admin-username-should-not-be-literal.md)
- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

リンター ルールの適用方法をカスタマイズできます。 既定の設定を上書きするには、**bicepconfig.json** ファイルを追加し、カスタム設定を適用します。 これらの設定の適用方法の詳細については、「[Bicep 構成ファイルにカスタム設定を追加する](bicep-config-linter.md)」を参照してください。

## <a name="use-in-visual-studio-code"></a>Visual Studio Code での使用

次のスクリーンショットは、Visual Studio Code でのリンターを示しています。

:::image type="content" source="./media/linter/bicep-linter-show-errors.png" alt-text="Visual Studio Code での Bicep リンターの使用状況。":::

以下のスクリーンショットに示されているように、 **[問題]** ペインには 4 つのエラーと、1 つの警告、1 つの情報メッセージがあります。  情報メッセージには、使用されている Bicep 構成ファイルが示されています。 構成ファイルで **verbose** を **true** に設定した場合、ここにはこの情報のみが表示されます。

問題のある領域のいずれかに、マウス カーソルを置きます。 リンターによって、エラーまたは警告に関する詳細が示されます。 領域を選択すると、青い電球も表示されます。

:::image type="content" source="./media/linter/bicep-linter-show-quickfix.png" alt-text="Visual Studio Code での Bicep リンターの使用状況- quickfix の表示。":::

青い電球または **Quick fix** リンクを選択し、ソリューションを表示します。

:::image type="content" source="./media/linter/bicep-linter-show-quickfix-solution.png" alt-text="Visual Studio Code での Bicep リンターの使用状況 - quickfix ソリューションの表示。":::

ソリューションを選択して、問題を自動的に修正します。

## <a name="use-in-bicep-cli"></a>Bicep CLI での使用

次のスクリーンショットは、コマンド ラインでのリンターを示しています。 build コマンドからの出力には、ルール違反が示されています。

:::image type="content" source="./media/linter/bicep-linter-command-line.png" alt-text="コマンド ラインでの Bicep リンターの使用方法。":::

これらのチェックは、CI/CD パイプラインの一部として統合できます。 GitHub アクションを使用して、bicep ビルドを試みることができます。 エラーにより、パイプラインが失敗します。

## <a name="next-steps"></a>次のステップ

* リンター ルールのカスタマイズ方法の詳細については、「[Bicep 構成ファイルにカスタム設定を追加する](bicep-config-linter.md)」を参照してください。
* Visual Studio Code と Bicep 拡張機能の使用方法の詳細については、「[クイック スタート: Visual Studio Code を使用して Bicep ファイルを作成する](./quickstart-create-bicep-use-visual-studio-code.md)」を参照してください。

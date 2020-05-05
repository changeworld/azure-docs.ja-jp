---
title: Automation アカウントのトラブルシューティング
description: Azure アカウントに関する問題のトラブルシューティングと解決方法について説明します。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 7b5e7171ac679384966e9dce79425cd1fa881c53
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679378"
---
# <a name="troubleshoot-the-automation-account"></a>Automation アカウントのトラブルシューティング

この記事では、Automation アカウントを使用する際に発生する可能性がある問題の解決方法について説明します。 次のセクションでは、具体的なエラー メッセージと、考えられる個別の解決策を示します。 Automation アカウントに関する一般的な情報については、[Azure アカウントの作成](../automation-quickstart-create-account.md)に関するページを参照してください。

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>シナリオ:サブスクリプションで Automation リソース プロバイダーを登録できない

### <a name="issue"></a>問題

Automation アカウントで管理ソリューションを使用するときに、次のエラーが発生します。

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>原因

Automation リソース プロバイダーがサブスクリプションに登録されていません。

### <a name="resolution"></a>解像度

Automation リソース プロバイダーを登録するには、Azure portal で次の手順に従います。

1. お使いのブラウザーで [Azure portal](https://portal.azure.com) に移動します。

2. **[サブスクリプション]** に移動し、[サブスクリプション] ページで、自分のサブスクリプションを選択します。   

3. **[設定]** で、 **[リソース プロバイダー]** を選択します。

4. リソース プロバイダーの一覧で、**Microsoft.Automation** リソース プロバイダーが登録されていることを確認します。

5. プロバイダーが一覧に表示されていない場合は、「[リソース プロバイダーの登録エラーの解決](/azure/azure-resource-manager/resource-manager-register-provider-errors)」に記載の手順に従ってそれを登録します。

## <a name="next-steps"></a>次のステップ

自分の問題が上記にない場合、または問題を解決できない場合は、追加のサポートを受けるために、次のいずれかのチャネルをお試しください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。
* Azure サポート インシデントを送信する。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
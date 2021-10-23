---
title: Azure Automation でローカル認証を無効にする
description: この記事では、Azure Automation でのローカル認証の無効化について説明します。
services: automation
ms.subservice: process-automation
ms.date: 09/28/2021
ms.topic: how-to
ms.openlocfilehash: 54da805b7f4c2332d0d475d2c5eb31b24fca597f
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165649"
---
# <a name="disable-local-authentication-in-automation"></a>Automation でローカル認証を無効にする

Azure Automation では、すべての Automation サービス パブリック エンドポイントでの Microsoft Azure Active Directory (Azure AD) 認証のサポートが提供されます。 この重要なセキュリティ強化によって、証明書の依存関係がなくなり、組織はローカル認証方法の無効化を制御できるようになります。 この機能により、ID の集中管理された制御および管理と Azure AD 経由のリソース資格情報が必要な場合のシームレスな統合が可能になります。

Azure Automation では、[ローカル認証を無効にするように Azure Automation アカウントを構成する](../automation/policy-reference.md#azure-automation)という Azure ポリシーを使用して Automation アカウント レベルで "ローカル認証を無効にする" ためのオプション機能が提供されます。 既定では、このフラグはアカウントで false に設定されているため、ローカル認証と Azure AD 認証の両方を使用できます。 ローカル認証を無効にすることを選択した場合、Automation サービスでは Azure AD ベースの認証のみを受け入れます。

Azure portal では、認証が無効になっていると、選択された Automation アカウントのランディング ページに警告メッセージが表示されることがあります。ローカル認証ポリシーが有効になっているかどうかを確認するには、PowerShell コマンドレット [Get-AzAutomationAccount](/powershell/module/az.automation/get-azautomationaccount) を使用し、プロパティ `DisableLocalAuth` を確認します。 `true` の値は、ローカル認証が無効になっていることを示します。

ローカル認証を無効にしても、すぐに効果が現れるわけではありません。 このサービスによって将来の認証要求がブロックされるようになるまで数分かかります。
 
## <a name="re-enable-local-authentication"></a>ローカル認証を再び有効にする

ローカル認証を再び有効にするには、パラメーター `-DisableLocalAuth false` を指定して PowerShell コマンドレット `Set-AzAutomationAccount` を実行します。このサービスによってローカル認証要求を許可する変更が受け入れられるまで数分かかります。 

## <a name="compatibility"></a>互換性

次の表では、ローカル認証を無効にすることによって機能しなくなる動作または機能について説明します。

|シナリオ | 代替 |
|---|---|
|webhook を使用した Runbook の起動。 | Azure AD 認証を使用する Azure Resource Manager テンプレートを使用して Runbook ジョブを起動します。 |
|Automation Desired State Configuration の使用。| [Azure Policy のゲスト構成](../governance/policy/concepts/guest-configuration.md)を使用します。  |
|エージェント ベースの Hybrid Runbook Worker の使用。| [拡張機能ベースの Hybrid Runbook Worker (プレビュー)](./extension-based-hybrid-runbook-worker-install.md) を使用します。|

## <a name="limitations"></a>制限事項

ローカル認証が無効になっていると、Update Management の修正プログラム適用は機能しません。


## <a name="next-steps"></a>次のステップ
- [Azure Automation アカウントの認証の概要](./automation-security-overview.md)

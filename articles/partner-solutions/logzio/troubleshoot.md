---
title: Logz.io のトラブルシューティング - Azure パートナー ソリューション
description: Azure と Logz.io の統合のトラブルシューティングを行う方法について説明します。
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 10/25/2021
ms.author: tomfitz
ms.openlocfilehash: 4322cdfead345aec836760199ce7d6f1f9654c59
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057516"
---
# <a name="troubleshoot-logzio-integration-with-azure"></a>Azure と Logz.io の統合のトラブルシューティング

この記事では、Azure と Logz.io の統合のトラブルシューティングを行う方法について説明します。

## <a name="owner-role-needed-to-create-resource"></a>リソースを作成するために必要な所有者ロール

Logz.io を設定するには、Azure サブスクリプションで[所有者ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)が割り当てられている必要があります。 この統合を開始する前に、[ご自分のアクセス権を確認](../../role-based-access-control/check-access.md)してください。

## <a name="single-sign-on-errors"></a>シングル サインオン エラー

SSO の設定中にエラーが発生する可能性があります。 シングル サインオン (SSO) を設定する方法の詳細については、「[Logz.io シングル サインオンの設定](setup-sso.md)」を参照してください。

### <a name="unable-to-save-single-sign-on-settings"></a>シングル サインオンの設定を保存できない

このエラーは、Logz.io Security Assertion Markup Language (SAML) 識別子を使用している別のエンタープライズ アプリケーションがあることを意味します。 そのアプリケーションを見つけるには、 **[基本的な SAML 構成]** セクションの **[編集]** を選択します。

この問題を解決するには、SAML を使用しているエンタープライズ アプリケーションを無効にするか、別のエンタープライズ アプリケーションを使用して Logz.io で SAML SSO を設定します。 アプリケーションに必要な設定があることを確認します。

### <a name="application-not-shown-in-single-sign-on"></a>シングル サインオンでアプリケーションが表示されない

"_アプリケーション ID_" で検索してみます。 結果が表示されない場合は、アプリケーションの SAML 設定を調べます。 グリッドには、正しい SAML 設定を持つ既存のアプリケーションのみが表示されます。 **[識別子]** と **[応答 URL]** は、次の図に示されているとおりにする必要があります。

空白のテキスト ボックスに、 **[識別子]** と **[応答 URL]** の新しい値を追加できます。

新しい値を追加するには、次のパターンを使用します。

- **[識別子]** : `urn:auth0:logzio:<Application ID>`
- **[応答 URL]** : `https://logzio.auth0.com/login/callback?connection=<Application ID>`

:::image type="content" source="./media/troubleshoot/basic-saml-config.png" alt-text="[基本的な SAML 構成] の設定。":::

## <a name="logs-not-being-sent-to-logzio"></a>Logz.io にログが送信されない

Logz.io にログを送信するのは、[Azure Monitor リソースのログ カテゴリ](../../azure-monitor/essentials/resource-logs-categories.md)の一覧に含まれているリソースだけです。

リソースが Logz.io にログを送信しているかどうかを確認するには:

1. 特定のリソースの [Azure 診断設定](../../azure-monitor/essentials/diagnostic-settings.md)に移動します。
1. Logz.io の診断設定があることを確認します。

:::image type="content" source="./media/troubleshoot/diagnostics.png" alt-text="診断設定。":::

## <a name="limit-reached-in-monitored-resources"></a>監視対象リソースで上限に達した

Azure Monitor 診断では、1 つのリソースまたはサブスクリプションで最大 5 つの診断設定がサポートされます。 この上限に達すると、そのリソースには **[監視対象リソース]** で **[上限に達しました]** と表示されます。 Logz.io を使用して監視を追加することはできません。

:::image type="content" source="./media/troubleshoot/limit-monitored-resources.png" alt-text="監視対象リソースの上限に達しました。":::

## <a name="vm-extension-installation-failed"></a>VM 拡張機能のインストールが失敗した

仮想マシン (VM) は、1 つの Logz.io アカウント (メインまたはサブ) でのみ監視できます。 別のアカウントによって既に監視されている VM にエージェントをインストールしようとすると、次のエラーが表示されます。

:::image type="content" source="./media/troubleshoot/vm-agent-fail.png" alt-text="仮想マシン エージェントのインストールに失敗しました。":::

## <a name="purchase-errors"></a>購入エラー

購入に失敗するのは、有効なクレジット カードが Azure サブスクリプションに接続されていないためです。 または、支払い方法がサブスクリプションに関連付けられていません。

購入エラーを解決するには:

- 別の Azure サブスクリプションを使用してください。
- サブスクリプションのクレジット カードまたは支払い方法を追加するか、更新してください。 詳細については、「[Azure のクレジット カードの追加または更新](../../cost-management-billing/manage/change-credit-card.md)」を参照してください。

**[操作の詳細]** を選択すると、リソースのデプロイ ページからエラーの出力を表示できます。

```json
{
  "status": "Failed",
  "error": {
    "code": "BadRequest",
    "message": "{\"message\":\"Purchase has failed because we couldn't find a valid credit card nor
               a payment method associated with your Azure subscription. Please use a different
               Azure subscription or add\\\\update current credit card or payment method for this
               subscription and retry.\",\"code\":\"BadRequest\"}"
  }
}
```

## <a name="next-steps"></a>次の手順

- Logz.io 統合を[管理する](manage.md)方法について説明します。
- SSO の詳細については、「[Logz.io シングル サインオンの設定](setup-sso.md)」を参照してください。

---
title: Microsoft Defender for Cloud のアラート メール通知を構成する
description: Microsoft Defender for Cloud のセキュリティ アラート メールを微調整する方法について説明します。
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: quickstart
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: 63123cf7dc7257578b4ddcf3afaf272f42d599d5
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525619"
---
# <a name="configure-email-notifications-for-security-alerts"></a>セキュリティ アラートの電子メール通知を構成する 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

セキュリティ アラートは、組織内の適切な人物に届ける必要があります。 既定では、サブスクリプションに関して重大度が高いアラートがトリガーされるたびに、Microsoft Defender for Cloud からサブスクリプションの所有者にメールが送信されます。 このページでは、これらの通知をカスタマイズする方法について説明します。

Defender for Cloud の **[電子メール通知]** 設定ページを使用して、次のような通知メールに関する設定を定義します。

- **通知する必要がある "*ユーザー*"** - 電子メールは、選択した個人、またはサブスクリプションに指定された Azure ロールを持つ任意のユーザーに送信できます。 
- **通知する必要がある "*内容*"** - Defender for Cloud から通知を送信する必要がある重大度レベルを変更します。

アラート疲れを防ぐために、Defender for Cloud は送信メールの量を制限します。 各サブスクリプションについて、Defender から次のようにメールが送信されます。

- **重大度が高い** アラートの場合、**6 時間** ごとに最大 1 通 (1 日あたり 4 通) のメール
- **重大度が中程度** のアラートの場合、**12 時間** ごとに最大 1 通 (1 日あたり 2 通) のメール
- **重大度が低い** アラートの場合、**24 時間** ごとに最大 1 通のメール

:::image type="content" source="./media/configure-email-notifications/email-notification-settings.png" alt-text="セキュリティ アラートに関する電子メールを受信する連絡先の詳細を構成しています。" :::
 
## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開 (GA)|
|価格:|Free|
|必要なロールとアクセス許可:|**Security Admin**<br>**サブスクリプションの所有者** |
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 国 (Azure Government、Azure China 21Vianet)|
|||


## <a name="customize-the-security-alerts-email-notifications-via-the-portal"></a>セキュリティ アラートの電子メール通知をポータルからカスタマイズする<a name="email"></a>
個人または特定の Azure ロールを持つすべてのユーザーにメール通知を送信できます。

1. Defender for Cloud の **[環境の設定]** 領域から、関連するサブスクリプションを選択し、 **[電子メール通知]** を選択します。

1. 次のいずれかまたは両方のオプションを使用して、通知の受信者を定義します。

    - ドロップダウン リストから、使用可能なロールを選択します。
    - 特定の電子メール アドレスをコンマ区切りで入力します。 入力できる電子メール アドレスの数に制限はありません。

1. セキュリティ連絡先の情報をサブスクリプションに適用するには、 **[保存]** を選択します。

## <a name="customize-the-alerts-email-notifications-through-the-api"></a>API を通じてアラートのメール通知をカスタマイズする
提供されている REST API を使用してメール通知を管理することもできます。 詳細については、[SecurityContacts API に関するドキュメント](/rest/api/securitycenter/securitycontacts)を参照してください。

これは、セキュリティ連絡先の構成を作成する際の PUT 要求本文の例です。

URI: `https://management.azure.com/subscriptions/<SubscriptionId>/providers/Microsoft.Security/securityContacts/default?api-version=2020-01-01-preview`

```json
{
    "properties": {
        "emails": admin@contoso.com;admin2@contoso.com,
        "notificationsByRole": {
            "state": "On",
            "roles": ["AccountAdmin", "Owner"]
        },
        "alertNotifications": {
            "state": "On",
            "minimalSeverity": "Medium"
        },
        "phone": ""
    }
}
```


## <a name="see-also"></a>関連項目
セキュリティ アラートの詳細については、次のページを参照してください。

- [セキュリティ アラート - リファレンス ガイド](alerts-reference.md) -- Microsoft Defender for Cloud の Threat Protection モジュールで表示される可能性のあるセキュリティ アラートについて学習します
- [Microsoft Defender for Cloud でのセキュリティ アラートの管理と対応](managing-and-responding-alerts.md) -- セキュリティ アラートの管理と対応の方法について説明しています。
- [ワークフローの自動化](workflow-automation.md) -- カスタム通知ロジックを使用してアラートに対する応答を自動化します

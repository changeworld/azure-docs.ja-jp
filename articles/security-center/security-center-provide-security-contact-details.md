---
title: Azure Security Center アラートの電子メール通知を構成する
description: Azure Security Center によって送信されるセキュリティ アラートの電子メールの種類を微調整する方法について学習します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: memildin
ms.openlocfilehash: 6b54f02b39e233dcf35f0d18682ca102883d76c3
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791870"
---
# <a name="configure-email-notifications-for-security-alerts"></a>セキュリティ アラートの電子メール通知を構成する 

セキュリティ アラートは、組織内の適切な人物に届ける必要があります。 既定では、サブスクリプションの重大度が高いアラートがトリガーされるたびに、Security Center からサブスクリプションの所有者に電子メールが送信されます。 このページでは、これらの通知をカスタマイズする方法について説明します。

通知用電子メールに独自の設定を定義するには、Azure Security Center の **[電子メール通知]** 設定ページで以下を選択できます。

- **通知する必要がある " _ユーザー_ "** - 電子メールは、選択した個人、またはサブスクリプションに指定された Azure ロールを持つ任意のユーザーに送信できます。 
- **通知する必要がある " _内容_ "** - Security Center から通知を送信する必要がある重大度レベルを変更します。

アラート疲れを避けるため、Security Center では送信メールの量が制限されています。 各サブスクリプションについて、Security Center では以下のように送信が行われます。

- **重要度が高い** アラートに関しては、1 日あたり最大 **4 通** のメール
- **重要度が中程度** のアラートに関しては、1 日あたり最大 **2 通** のメール
- **重要度が低い** アラートに関しては、1 日あたり最大 **1 通** のメール

:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="セキュリティ アラートに関する電子メールを受信する連絡先の詳細を構成しています。" :::
 
## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般提供 (GA)|
|価格:|Free|
|必要なロールとアクセス許可:|**Security Admin**<br>**サブスクリプションの所有者** |
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||


## <a name="customize-the-security-alerts-email-notifications"></a>セキュリティ アラートの電子メール通知をカスタマイズする<a name="email"></a>

個人または特定の Azure ロールを持つすべてのユーザーにメール通知を送信できます。

1. Security Center の **[価格と設定]** 領域から、関連するサブスクリプションを選択し、 **[電子メール通知]** を選択します。

1. 次のいずれかまたは両方のオプションを使用して、通知の受信者を定義します。

    - ドロップダウン リストから、使用可能なロールを選択します。
    - 特定の電子メール アドレスをコンマ区切りで入力します。 入力できる電子メール アドレスの数に制限はありません。

1. セキュリティ連絡先の情報をサブスクリプションに適用するには、 **[保存]** を選択します。


## <a name="next-steps"></a>次のステップ

セキュリティ アラートの詳細については、次のページを参照してください。

- [セキュリティ アラート - リファレンス ガイド](alerts-reference.md) -- Azure Security Center の Threat Protection モジュールで表示される可能性のあるセキュリティ アラートについて学習します
- [Azure Security Center でのセキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md) -- セキュリティ アラートの管理と対応の方法について学習します
- [Security Center のトリガーへの応答を自動化する](workflow-automation.md) -- オートメーションを使用して、Security Center のトリガーにカスタム通知ロジックで応答します
---
title: Azure Security Center でセキュリティ連絡先の詳細情報を指定する | Microsoft Docs
description: このドキュメントでは、Azure Security Center でセキュリティ連絡先の詳細情報を指定する方法について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: 661d3845365778f7ef23cdd05b81b98c3bf84259
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86519287"
---
# <a name="set-up-email-notifications-for-security-alerts"></a>セキュリティ アラートのメール通知を設定する 

組織内の適切なユーザーが環境内のセキュリティ アラートについて通知を受け取るようにするには、 **[メール通知]** 設定ページでメール アドレスを入力します。

通知を設定するときに、特定の個人またはサブスクリプションの特定の RBAC ロールを持つユーザーにメールを送信するように構成できます。 

アラート疲れを避けるため、Security Center では送信メールの量が制限されています。 各サブスクリプションについて、Security Center では以下のように送信が行われます。

- **重要度が高い**アラートに関しては、1 日あたり最大 **4 通**のメール
- **重要度が中程度**のアラートに関しては、1 日あたり最大 **2 通**のメール
- **重要度が低い**アラートに関しては、1 日あたり最大 **1 通**のメール

## <a name="availability"></a>可用性

- リリース状態: **一般提供**
- 必要なロール: **セキュリティ管理者**または**サブスクリプションの所有者** 
- クラウド: ✔商用クラウド ✔ US Gov (一部) ✘ ナショナル/ソブリン (China Gov、その他の Gov)


## <a name="set-up-email-notifications-for-alerts"></a>アラートの電子メール通知を設定する<a name="email"></a>

個人または特定の RBAC ロールを持つすべてのユーザーにメール通知を送信できます。

1. Security Center の **[Pricing & settings]\(価格と設定\)** 領域の関連するサブスクリプションで、 **[メール通知]** を選択します。

1. 以下のように通知の受信者を定義します。

    - ドロップダウン リストから、使用可能なロールを選択します。
    - または、特定のメール アドレスをコンマ区切りで入力します。 入力できる電子メール アドレスの数に制限はありません。

1. セキュリティ連絡先の情報をサブスクリプションに適用するには、 **[保存]** を選択します。


## <a name="see-also"></a>関連項目
セキュリティ アラートの詳細については、次を参照してください。

* [セキュリティ アラート - リファレンス ガイド](alerts-reference.md) - Azure Security Center の Threat Protection モジュールで表示される可能性のあるセキュリティ アラートについて説明しています
* [Azure Security Center でのセキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md): セキュリティ アラートの管理と対応の方法について説明しています
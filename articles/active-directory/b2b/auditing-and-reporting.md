---
title: Azure Active Directory B2B コラボレーション ユーザーの監査およびレポート | Microsoft Docs
description: ゲスト ユーザーは、Azure Active Directory B2B コラボレーションで構成できます
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: fbe1cfdcfb1b5ec295748c3c77030df45323ee54
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53434326"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>B2B コラボレーション ユーザーの監査およびレポート
ゲスト ユーザーに対しても、メンバー ユーザーに対する場合と同様に、監査機能を使用できます。 

## <a name="access-reviews"></a>アクセス レビュー
アクセス レビューを使用すると、ゲスト ユーザーがリソースへのアクセスをまだ必要としているかどうかを定期的に確認できます。 **アクセス レビュー**機能は、**Azure Active Directory** では **[管理]** > **[組織の関係]** から利用できます。 (Azure portal で **[All services]\(すべてのサービス\)** から "access reviews" を検索することもできます。)アクセス レビューの使用方法については、「[Azure AD のアクセス レビューによるゲスト アクセスの管理](../governance/manage-guest-access-with-access-reviews.md)」を参照してください。

## <a name="audit-logs"></a>監査ログ

Azure AD 監査ログはシステムとユーザーのアクティビティのレコードを提供しますが、これにはゲスト ユーザーによって開始されたアクティビティも含まれます。 監査ログにアクセスするには、**Azure Active Directory** で、**[Monitoring]\(監視\)** にある **[監査ログ]** を選択します。 招待された Sam Oogle の招待と使用の履歴の例を示します。

![監査ログ](./media/auditing-and-reporting/audit-log.png)

各イベントに立ち入って、詳細を取得することができます。 たとえば、承認の詳細を見てみましょう。

![活動の詳細](./media/auditing-and-reporting/activity-details.png)

これらのログを Azure AD からエクスポートし、任意のレポート ツールを使用してレポートをカスタマイズすることもできます。

### <a name="next-steps"></a>次の手順

- [B2B コラボレーション ユーザーのプロパティ](user-properties.md)


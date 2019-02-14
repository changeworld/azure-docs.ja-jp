---
title: Azure Active Directory B2B コラボレーション ユーザーの監査およびレポート | Microsoft Docs
description: ゲスト ユーザーは、Azure Active Directory B2B コラボレーションで構成できます
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fc36128f8f998d78dd2cf9ef112fe5961bbef5b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204612"
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


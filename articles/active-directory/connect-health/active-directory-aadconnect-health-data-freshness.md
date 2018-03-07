---
title: "Azure AD Connect Health - アラート \"Health サービス データが最新ではありません\" | Microsoft Docs"
description: "このドキュメントでは、アラート \"Health サービス データが最新ではありません\" の原因と、そのトラブルシューティングを実行する方法について説明します。"
services: active-directory
documentationcenter: 
author: zhiweiw
manager: maheshu
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: 45e88320a64770239c8f322212e12ca7826cd0da
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>アラート "Health サービス データが最新ではありません"

## <a name="overview"></a>概要
<li>Azure AD Connect Health では、2 時間の間にサーバーから一部のデータ ポイントが送られなかった場合に、このデータの更新状態についてのアラートが生成されます。 このアラートのタイトルは、「**Health サービス データが最新ではありません**」です。 </li>
<li>2 時間の間にサーバーから送信されたデータ要素の一部を Connect Health が受信しなかった場合に、**警告**状態通知が発生します。 警告状態通知では、テナント管理者への電子メール通知はトリガーされません。 </li>
<li>2 時間の間にサーバーから送信されたデータ要素を Connect Health がまったく受信しなかった場合に、**エラー**状態通知が発生します。 エラー状態通知では、テナント管理者への電子メール通知がトリガーされます。 </li>

>[!IMPORTANT] 
> このアラートは、Connect Health [データ リテンション期間ポリシー](active-directory-aadconnect-health-gdpr.md#data-retention-policy)に従います。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順 
* [要件セクション](active-directory-aadconnect-health-agent-install.md#requirements)を確認して、必ず要件に対応してください。
* [テスト接続ツール](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)を使用して接続の問題を検出します。


## <a name="next-steps"></a>次の手順
* [Azure AD Connect Health の FAQ](active-directory-aadconnect-health-faq.md)

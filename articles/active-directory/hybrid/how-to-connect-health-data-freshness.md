---
title: Azure AD Connect Health - アラート "Health サービス データが最新ではありません" | Microsoft Docs
description: このドキュメントでは、アラート "Health サービス データが最新ではありません" の原因と、そのトラブルシューティングを実行する方法について説明します。
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: 430ea5f0a6f737d7632a4352c24d893368b80558
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2018
ms.locfileid: "46310381"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>アラート "Health サービス データが最新ではありません"

## <a name="overview"></a>概要
<li>Azure AD Connect Health では、2 時間の間にサーバーから一部のデータ ポイントが送られなかった場合に、このデータの更新状態についてのアラートが生成されます。 このアラートのタイトルは、「**Health サービス データが最新ではありません**」です。 </li>
<li>2 時間の間にサーバーから送信されたデータ要素の一部を Connect Health が受信しなかった場合に、**警告**状態通知が発生します。 警告状態通知では、テナント管理者への電子メール通知はトリガーされません。 </li>
<li>2 時間の間にサーバーから送信されたデータ要素を Connect Health がまったく受信しなかった場合に、**エラー**状態通知が発生します。 エラー状態通知では、テナント管理者への電子メール通知がトリガーされます。 </li>

>[!IMPORTANT] 
> このアラートは、Connect Health [データ リテンション期間ポリシー](reference-connect-health-user-privacy.md#data-retention-policy)に従います。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順 
* [要件セクション](how-to-connect-health-agent-install.md#requirements)を確認して、必ず要件に対応してください。
* [テスト接続ツール](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)を使用して接続の問題を検出します。


## <a name="next-steps"></a>次の手順
* [Azure AD Connect Health の FAQ](reference-connect-health-faq.md)

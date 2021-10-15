---
title: Azure AD サインイン ログの基本情報識別子 | Microsoft Docs
description: 基本情報識別子の用途について説明します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/30/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ca1ea9524a10ac46a33f6430228d8a45ee66f28
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367483"
---
# <a name="basic-info-identifiers-in-the-azure-ad-sign-in-log"></a>サインイン ログの基本情報識別子

Azure AD は、コンプライアンスのためにすべてのサインインのログを Azure テナントに記録します。 IT 管理者は、ログの値を正しく解釈できるように、サインイン ログの値の意味を把握しておく必要があります。
この記事では、サインイン ログの [基本情報] タブの識別子について説明します。

## <a name="unique-identifiers"></a>一意識別子 

Azure AD では、リソース アクセスには 3 つの関連するコンポーネントがあります。

- **対象者** -サインインを実行する本人 (ユーザー)。 
- **方法** - アクセスに使用されるクライアント (アプリケーション)。  
- **内容** - 本人によってアクセスされるターゲット (リソース)。


各コンポーネントには一意識別子 (ID) が関連付けられています。 Windows Azure Service Management API を使用して Azure portal にアクセスするユーザーの例を次に示します。

![監査ログを開く](./media/reference-basic-info-identifiers/sign-in-details-basic-info.png)

## <a name="tenant-identifiers"></a>テナント識別子

サインイン ログは、次の 2 つのテナント識別子を追跡します。

- **ホーム テナント** -ユーザー ID を所有するテナント。 
- **リソース テナント** - (ターゲット) リソースを所有するテナント。

これらの識別子は、クロステナントのシナリオに関連しています。 たとえば、テナントの外部のユーザーがリソースにアクセスしていることを確認するには、ホーム テナントがリソース テナントと一致しないすべてのエントリを選択します。

## <a name="request-id"></a>要求 ID

要求 ID は、発行されたトークンに対応する識別子です。 特定のトークンを使用して実行されたサインインを探す場合は、最初にトークンから要求 ID を抽出する必要があります。


## <a name="correlation-id"></a>関連付け ID

関連付け ID は、同じサインイン セッションからのサインインを関連付ける識別子です。 この識別子は便宜上実装されています。 この値はクライアントによって渡されたパラメーターに基づいているため、その精度は保証されません。 




## <a name="next-steps"></a>次のステップ

* [Azure Active Directory のサインイン ログ](concept-sign-ins.md)
* [Azure AD におけるサインイン診断とは](overview-sign-in-diagnostics.md)

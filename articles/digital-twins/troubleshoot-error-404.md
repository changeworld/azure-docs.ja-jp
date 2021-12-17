---
title: '失敗したサービス要求のトラブルシューティング: エラー 404 (サブドメインが見つかりません)'
titleSuffix: Azure Digital Twins
description: Azure Digital Twins からエラーの状態として 404 (サブドメインが見つかりません) が返されたとき、それを診断し、解決する方法について説明します。
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 9/23/2021
ms.openlocfilehash: 91125140632d689eaab18a74b7da75fe3c69800b
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132248"
---
# <a name="troubleshooting-failed-service-request-error-404-sub-domain-not-found"></a>失敗したサービス要求のトラブルシューティング: エラー 404 (サブドメインが見つかりません)

この記事では、Azure Digital Twins へのサービス要求から 404 エラーを受信した場合の原因と解決手順について説明します。 

## <a name="symptoms"></a>現象

このエラーは、Azure Digital Twins インスタンスとは別の [Azure Active Directory (Azure AD) テナント](../active-directory/develop/quickstart-create-new-tenant.md)に属しているサービス プリンシパルまたはユーザー アカウントを使用してインスタンスにアクセスするときに発生する可能性があります。 正しい[ロール](concepts-security.md)が ID に割り当てられているように見えますが、API 要求はのエラー状態 `404 Sub-Domain not found` で失敗します。

## <a name="causes"></a>原因

### <a name="cause-1"></a>原因 #1

Azure Digital Twins では、すべての認証ユーザーが Azure Digital Twins インスタンスと同じ Azure AD テナントに属している必要があります。

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

## <a name="solutions"></a>ソリューション

### <a name="solution-1"></a>解決策 #1

この問題を解決するには、別のテナントの各フェデレーション ID で Azure Digital Twins インスタンスの "ホーム" テナントからの **トークン** を要求します。 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

### <a name="solution-2"></a>解決策 #2

コードで `DefaultAzureCredential` クラスを使用していて、トークンを取得した後もこの問題が引き続き発生する場合は、認証の既定値が別の種類に設定される場合でもテナントを明確にするための `DefaultAzureCredential` オプションでホーム テナントを指定できます。

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="next-steps"></a>次のステップ

Azure Digital Twins のセキュリティとアクセス許可の詳細を確認します。
* [Azure Digital Twins ソリューションのセキュリティ](concepts-security.md)

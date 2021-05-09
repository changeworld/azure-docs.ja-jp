---
title: Azure Digital Twins の要求が状態 404 Sub-Domain not found で失敗した
description: "'サービス要求が失敗しました。 Azure Digital Twins での状態 404 Sub-Domain not found"
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/13/2021
ms.openlocfilehash: bc635d742fb9edf53a6cd3244b7e8aa34c8d7148
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108205659"
---
# <a name="service-request-failed-status-404-sub-domain-not-found"></a>サービス要求が失敗しました。 状態: 404 Sub-Domain not found

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
* [概念:Azure Digital Twins ソリューションのセキュリティ](concepts-security.md)

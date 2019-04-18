---
title: Media Services アカウント用のロールベースのアクセス制御 - Azure | Microsoft Docs
description: この記事では、Azure Media Services アカウント用のロールベースのアクセス制御 (RBAC) について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 212d7f5352b58ee8f5b2c119bb1f5f828591f6bf
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471786"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Media Services アカウント用のロールベースのアクセス制御 (RBAC)

現在、Azure Media Services では、サービスに固有のカスタム ロールは定義されていません。 お客様は、組み込みロールである**所有者**または**共同作成者**を使用して、Media Services アカウントへのフル アクセスを取得できます。 これらのロールの主な違いとして、**所有者**はリソースにアクセスできるユーザーを制御できますが、**共同作成者**はできません。 組み込みの閲覧者アカウントは、Media Services アカウントへの読み取りアクセス権のみを持っています。 

## <a name="design-principles"></a>設計原則

v3 API の主要な設計原則の 1 つは、API の安全性の向上です。 v3 API シリーズは、**Get** または **List** 操作でシークレットまたは資格情報を返しません。 キーは常に、null または空であるか、応答から削除されます。 ユーザーがシークレットまたは資格情報を取得するには、別のアクション メソッドを呼び出す必要があります。 **閲覧者**ロールでは、操作を呼び出せないため、Asset.ListContainerSas、StreamingLocator.ListContentKeys、ContentKeyPolicies.GetPolicyPropertiesWithSecrets などの操作を呼び出すことはできません。 別々のアクションを使用することで、必要に応じてカスタム ロールに、よりきめ細かな RBAC セキュリティ アクセス許可を設定できます。

Media Services でサポートされている操作を一覧表示するには、次を実行します。

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

[組み込みロールの定義](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)に関する記事では、ロールで許可されている内容が詳しく説明されています。 

詳細については、次の記事を参照してください。

- [従来のサブスクリプション管理者ロール、Azure RBAC ロール、および Azure AD 管理者ロール](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Azure リソースの RBAC の概要](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [RBAC を使用してアクセスを管理する](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Media Services .NET を使用してコンテンツ キー ポリシーを取得する](get-content-key-policy-dotnet-howto.md)

## <a name="next-steps"></a>次の手順

[Media Services v3 API シリーズを使用した開発](media-services-apis-overview.md)

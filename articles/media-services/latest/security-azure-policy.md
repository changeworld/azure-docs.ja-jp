---
title: Media Services での Azure Policy の組み込みサポート
description: この記事では、Azure Media Services シナリオ向けの Azure Policy の組み込みサポートについて説明します。
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: johndeu
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 88b1907d0c767d77dd4a1f2e38ab859d4147ea96
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429828"
---
# <a name="azure-policy-for-media-services"></a>Media Services 用の Azure Policy

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services には、組織の標準とコンプライアンスを大規模に適用するのに役立つ組み込みの [Azure Policy](../../governance/policy/overview.md) 定義がいくつか用意されています。
Azure Policy の一般的なユースケースには、リソースの整合性、規制コンプライアンス、セキュリティ、コスト、管理のガバナンスの実装が含まれています。

Media Services には、作業の開始に役立つように組み込まれている Azure Policy の一般的なユースケースの定義がいくつか用意されています。

## <a name="built-in-azure-policy-definitions-for-media-services"></a>Media Services 用の組み込みの Azure Policy 定義

Media Services で使用して作業を開始するのに役立つ組み込みのポリシー ユース ケースの定義がいくつか提供されています。これにより、独自のカスタム ポリシーを定義することもできます。

[!INCLUDE [Azure Policy Media Services](../../../includes/policy/reference/bycat/policies-media-services.md)]

[Media Services の組み込みポリシー定義のリスト](../../governance/policy/samples/built-in-policies.md#media-services)には、最新の定義が記載されています。また、コードの定義と、ポータルでそれらにアクセスする方法とがリンクされています。

## <a name="common-scenarios-that-require-azure-policy"></a>Azure Policy を必要とする一般的なシナリオ

* すべての Media Services アカウントを確実に Private Link で作成することがエンタープライズ セキュリティで求められている場合は、ポリシー定義を使用することで、(レガシ REST v2 API へのアクセスを無効にし、Private Link 機能にアクセスするために) 2020-05-01 API (またはそれ以降) のみを使用してアカウントを確実に作成することができます。
* コンテンツ キー ポリシーに使用されるトークンに特定のオプションを適用する場合は、特定の要件をサポートするように Azure Policy 定義を構築できます。
* セキュリティの目標により、ジョブ入力ソースは信頼できるストレージ アカウントからのみ取得するように制限し、外部 HTTP 入力へのアクセスは JobInputHttp を使用するように制限することが求められている場合は、Azure ポリシーを構築して入力 URI パターンを制限することができます。

## <a name="example-policy-definitions"></a>ポリシー定義の例

Azure Media Services では、Azure Policy 定義のサンプル セットを GitHub にて管理および発行します。
[Media Services 用の組み込みポリシー定義](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policyDefinitions/Media%20Services)のサンプルは、azure-policy GitHub リポジトリにあります。

詳細については、次の記事を参照してください。

- [Azure Policy とは](../../governance/policy/overview.md)
- [クイックスタート: ポータルでポリシーを作成する](../../governance/policy/assign-policy-portal.md)
- [Media Services 用の組み込みポリシー定義のリスト](../../governance/policy/samples/built-in-policies.md#media-services)

## <a name="next-steps"></a>次のステップ

- [Media Services v3 API シリーズを使用した開発](media-services-apis-overview.md)
- [Media Services でのロール ベースのアクセス制御](security-rbac-concept.md)
- [Media Services でのプライベート リンクの使い方](security-private-link-how-to.md)
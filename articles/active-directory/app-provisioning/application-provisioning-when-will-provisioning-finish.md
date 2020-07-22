---
title: 長時間を要する Azure AD ギャラリー アプリへのユーザー プロビジョニング
description: アプリケーションへのプロビジョニングを実行する理由を理解する方法は、想像以上に時間がかかる可能性があります。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: f0f572e5153b6e79bf7731d0d57b66e601a0054c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84781975"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>長時間を要する Azure AD ギャラリー アプリケーションへのユーザー プロビジョニング

最初にアプリケーションの自動プロビジョニングを有効にすると、初回サイクルに 20 分から数時間かかることがあります。この時間は Azure AD のディレクトリ サイズと、プロビジョニングの対象となるユーザー数によって異なります。 

初回サイクル後の両方のシステムの状態を表すウォーターマークがプロビジョニング サービスに保存されるため、後続の同期はより速くなり、パフォーマンスが改善されます。

## <a name="how-to-improve-provisioning-performance"></a>プロビジョニングのパフォーマンスを改善する方法

初回サイクルに数時間以上かかる場合、パフォーマンスを改善するために実行できる方法の 1 つが、

-   **ユーザーのスコープ フィルター**です。 スコープ フィルターによって、ユーザーが特定の属性値に基づいてフィルター処理され、プロビジョニング サービスが Azure AD から抽出するデータを細かく調整できるようになります。 スコープ フィルターの詳細については、「[スコープ フィルターを使用した属性ベースのアプリケーション プロビジョニング](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](user-provisioning.md)


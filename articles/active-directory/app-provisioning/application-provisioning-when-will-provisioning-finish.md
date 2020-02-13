---
title: 長時間を要する Azure AD ギャラリー アプリへのユーザー プロビジョニング
description: アプリケーションへのプロビジョニングを実行する理由を理解する方法は、想像以上に時間がかかる可能性があります。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c5cc2c47eafba7d6afdf506eab03a17aa8987be
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2020
ms.locfileid: "77065894"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>長時間を要する Azure AD ギャラリー アプリケーションへのユーザー プロビジョニング

最初にアプリケーションの自動プロビジョニングを有効にすると、初回サイクルに 20 分から数時間かかることがあります。この時間は Azure AD のディレクトリ サイズと、プロビジョニングの対象となるユーザー数によって異なります。 

初回サイクル後の両方のシステムの状態を表すウォーターマークがプロビジョニング サービスに保存されるため、後続の同期はより速くなり、パフォーマンスが改善されます。

## <a name="how-to-improve-provisioning-performance"></a>プロビジョニングのパフォーマンスを改善する方法

初回サイクルに数時間以上かかる場合、パフォーマンスを改善するために実行できる方法の 1 つが、

-   **ユーザーのスコープ フィルター**です。 スコープ フィルターによって、ユーザーが特定の属性値に基づいてフィルター処理され、プロビジョニング サービスが Azure AD から抽出するデータを細かく調整できるようになります。 スコープ フィルターの詳細については、「[スコープ フィルターを使用した属性ベースのアプリケーション プロビジョニング](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](user-provisioning.md)


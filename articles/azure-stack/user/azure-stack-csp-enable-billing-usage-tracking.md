---
title: クラウド サービス プロバイダーが Azure Stack サブスクリプションを管理できるようにする | Microsoft Docs
description: サービス プロバイダーが Azure Stack のサブスクリプションにアクセスできるようにします。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/19/2019
ms.openlocfilehash: 5d7398853e20702aef450e2532784f0dca7aac57
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246604"
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>クラウド サービス プロバイダーが Azure Stack サブスクリプションを管理できるようにする

*適用対象:Azure Stack 統合システム*

クラウド サービス プロバイダー (CSP) で Azure Stack を使用している場合は、Azure および Azure Stack 内のリソースにアクセスするために独自のサブスクリプションを管理するように選択できます。 プロバイダーに自分のサブスクリプションの管理を委託することもできます。 この記事では、その方法について説明します。

* 自分のサブスクリプションへのアクセス権をサービス プロバイダーに付与する。
* サービス プロバイダーが自分のサービスを管理できることを確認する。

> [!NOTE]
> 自分のアカウントが CSP で管理されておらず、次の手順をスキップすると、CSP で自分の Azure Stack サブスクリプションを管理することはできません。

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>クラウド サービス プロバイダーでサブスクリプションを管理する

CSP を**ユーザー**としてサブスクリプションに追加します。

1. CSP を、ユーザー ロールを持つゲスト ユーザーとしてテナント ディレクトリに追加します。 ユーザーを追加する手順については、[Azure Active Directory への新しいユーザーの追加](../../active-directory/add-users-azure-active-directory.md)に関するページを参照してください。
2. CSP はユーザーに代わってローカル Azure Stack サブスクリプションを作成します。 これで、Azure Stack の使用を開始する準備ができました。
3. CSP は、サブスクリプション内にリソースを作成して、その CSP もリソースを管理できることを確認する必要があります。 たとえば、[Azure Stack ポータルで Windows 仮想マシンを作成する](azure-stack-quick-windows-portal.md)ことができます。

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>クラウド サービス プロバイダーが RBAC 権限を使用してサブスクリプションを管理できるようにする

CSP を**所有者**としてサブスクリプションに追加します。

1. CSP を、ゲスト ユーザーとしてテナント ディレクトリに追加します。 ユーザーを追加する手順については、[Azure Active Directory への新しいユーザーの追加](../../active-directory/add-users-azure-active-directory.md)に関するページを参照してください。
2. CSP ゲスト ユーザーに**所有者**ロールを追加します。 サブスクリプションに CSP ユーザーを追加する手順については、[ロールベースのアクセス制御を使用した Azure サブスクリプション リソースへのアクセスの管理](../../role-based-access-control/role-assignments-portal.md)に関するページを参照してください。 CSP はユーザーに代わってローカル Azure Stack サブスクリプションを作成します。 これで、Azure Stack の使用を開始する準備ができました。
3. CSP は、サブスクリプション内にリソースを作成して、その CSP がリソースを管理できることを確認する必要があります。

## <a name="next-steps"></a>次の手順

* Azure Stack からリソース使用量情報を取得する方法ついて詳しくは、「[Azure Stack での使用量と請求](../azure-stack-billing-and-chargeback.md)」をご覧ください。

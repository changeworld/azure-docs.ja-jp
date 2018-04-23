---
title: クラウド サービス プロバイダーが Azure Stack サブスクリプションを管理できるようにする | Microsoft Docs
description: サービス プロバイダーが Azure Stack のサブスクリプションにアクセスできるようにします。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 26ba68be6d4932da77befaf7c968525393c0a033
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>クラウド サービス プロバイダーが Azure Stack サブスクリプションを管理できるようにする

*適用対象: Azure Stack 統合システム*

クラウド サービス プロバイダー (CSP) で Azure Stack を使用している場合は、Azure サブスクリプションや Azure Stack 内のリソースへのアクセスはそのプロバイダーが管理できます。 または、ユーザーは自分のサブスクリプションを管理できます。 この記事では、サービス プロバイダーがユーザーに代わってサブスクリプションにアクセスしたり、サービス プロバイダーがサービスを管理したりできるようにする方法について説明します。

> [!Note]  
>  次の手順が省略され、かつ CSP がまだユーザーのアカウントを管理していない場合、CSP はユーザーに代わって Azure Stack サブスクリプションを管理できません。

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>クラウド サービス プロバイダーでサブスクリプションを管理する

1. CSP を、ユーザー ロールを持つゲスト ユーザーとしてテナント ディレクトリに追加します。  ユーザーを追加する手順については、[Azure Active Directory への新しいユーザーの追加](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)に関するページを参照してください。
2. それにより、CSP はユーザーに代わってローカル Azure Stack サブスクリプションを作成します。
3. これで、Azure Stack の使用を開始する準備ができました。
3. CSP は次に、サブスクリプション内にリソースを作成して、その CSP もリソースを管理できることを確認する必要があります。 たとえば、[Azure Stack ポータルで Windows 仮想マシンを作成する](azure-stack-quick-windows-portal.md)ことができます。

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>クラウド サービス プロバイダーが RBAC 権限を使用してサブスクリプションを管理できるようにする

CSP を所有者としてサブスクリプションに追加します。 

1. CSP を、所有者ロールを持つ ゲスト ユーザーとしてテナント ディレクトリに追加します。  ユーザーを追加する手順については、[Azure Active Directory への新しいユーザーの追加](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)に関するページを参照してください。
2. CSP ゲスト ユーザーに所有者ロールを追加します。 サブスクリプションに CSP ユーザーを追加する手順については、[ロールベースのアクセス制御を使用した Azure サブスクリプション リソースへのアクセスの管理](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)に関するページを参照してください。
3. それにより、CSP はユーザーに代わってローカル Azure Stack サブスクリプションを作成します。
4. これで、Azure Stack の使用を開始する準備ができました。
5. CSP は次に、サブスクリプション内にリソースを作成して、その CSP がリソースを管理できることを確認する必要があります。 

## <a name="next-steps"></a>次の手順

  - Azure Stack からリソース使用量情報を取得する方法ついて詳しくは、「[Azure Stack での使用量と請求](../azure-stack-billing-and-chargeback.md)」をご覧ください。

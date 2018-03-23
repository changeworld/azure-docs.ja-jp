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
ms.openlocfilehash: 4bc5644425aa11fb210d81095e4166baefc6432e
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2018
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
2. CSP ゲスト ユーザーに所有者ロールを追加します。 サブスクリプションに CSP ユーザーを追加する手順については、[ロールベースのアクセス制御を使用した Azure サブスクリプション リソースへのアクセスの管理](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)に関するページを参照してください。
3. それにより、CSP はユーザーに代わってローカル Azure Stack サブスクリプションを作成します。
4. これで、Azure Stack の使用を開始する準備ができました。
5. CSP は次に、サブスクリプション内にリソースを作成して、その CSP がリソースを管理できることを確認する必要があります。 

## <a name="next-steps"></a>次の手順

  - リソース使用状況の情報を Azure Stack から取得する方法の詳細については、[Azure Stack での使用状況と課金](../azure-stack-billing-and-chargeback.md)に関するページを参照してください。

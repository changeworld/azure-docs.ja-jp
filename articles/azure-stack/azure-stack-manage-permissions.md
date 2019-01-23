---
title: Azure Stack でユーザーごとにリソースへのアクセス許可を管理する (サービス管理者およびテナント) | Microsoft Azure
description: サービス管理者またはテナントとして、RBAC アクセス許可を管理する方法を説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 26ce7139b856fc2f8d7d2cad549b3dd3c0f5e406
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304688"
---
# <a name="manage-role-based-access-control"></a>ロールベースのアクセス制御の管理

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

Azure Stack のユーザーは、サブスクリプション、リソース グループ、またはサービスの各インスタンスの閲覧者、所有者、または共同作業者になることができます。 たとえば、ユーザー A にはサブスクリプション 1 に対する閲覧者アクセス許可と、仮想マシン 7 に対する所有者アクセス許可がある可能性があります。

 - 閲覧者: ユーザーはすべてを閲覧できますが、変更を加えることはできません。
 - 共同作成者: ユーザーはリソースへのアクセス以外のすべてを管理できます。
 - Owner:ユーザーは、リソースへのアクセスを含め、すべてを管理できます。

## <a name="set-access-permissions-for-a-user"></a>ユーザーのアクセス権限の設定

1. 管理するリソースへの所有者アクセス許可があるアカウントでサインインします。
2. リソースのブレードで、**[アクセス]** アイコン ![](media/azure-stack-manage-permissions/image1.png) をクリックします。
3. **[ユーザー]** ブレードで、**[役割]** をクリックします。
4. **[役割]** ブレードで、**[追加]** をクリックし、ユーザーのアクセス許可を追加します。

## <a name="set-access-permissions-for-a-universal-group"></a>ユニバーサル グループのアクセス許可を設定する 

> [!Note]  
Active Directory フェデレーション サービス (AD FS) にのみ適用されます。

1. 管理するリソースへの所有者アクセス許可があるアカウントでサインインします。
2. リソースのブレードで、**[アクセス]** アイコン ![](media/azure-stack-manage-permissions/image1.png) をクリックします。
3. **[ユーザー]** ブレードで、**[役割]** をクリックします。
4. **[役割]** ブレードで、**[追加]** をクリックして、ユニバーサル グループ Active Directory グループのアクセス許可を追加します。

## <a name="next-steps"></a>次の手順
[Azure Stack テナントの追加](azure-stack-add-new-user-aad.md)


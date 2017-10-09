---
title: "Azure Stack でユーザーごとにリソースへのアクセス許可を管理する | Microsoft Azure"
description: "サービス管理者またはテナントとして、RBAC アクセス許可を管理する方法を説明します。"
services: azure-stack
documentationcenter: 
author: Heathl17
manager: byronr
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 1e0ced4b2d65456034ba3f8543152702c089a0b0
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---
# <a name="manage-role-based-access-control"></a>ロールベースのアクセス制御の管理

*適用対象: Azure Stack 統合システムおよび Azure Stack 開発キット*

Azure Stack のユーザーは、サブスクリプション、リソース グループ、またはサービスの各インスタンスの閲覧者、所有者、または共同作業者になることができます。 たとえば、ユーザー A にはサブスクリプション 1 に対する閲覧者アクセス許可と、仮想マシン 7 への所有者アクセス許可があるといったことが考えられます。

* 閲覧者: すべてを閲覧できますが、変更を加えることはできません。
* 共同作業者: リソースへのアクセス以外のすべてを管理できます
* 所有者: リソースへのアクセスを含め、すべてを管理できます。

## <a name="set-access-permissions-for-a-user"></a>ユーザーのアクセス権限の設定
1. 管理するリソースへの所有者アクセス許可があるアカウントでサインインします。
2. リソースのブレードで、**[アクセス]** アイコン ![](media/azure-stack-manage-permissions/image1.png) をクリックします。
3. **[ユーザー]** ブレードで、**[役割]** をクリックします。
4. **[役割]** ブレードで、**[追加]** をクリックし、ユーザーのアクセス許可を追加します。

## <a name="next-steps"></a>次のステップ




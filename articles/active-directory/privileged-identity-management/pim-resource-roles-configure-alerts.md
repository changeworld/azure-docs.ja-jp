---
title: Privileged Identity Management を使用して Azure リソースのロールのセキュリティ アラートを管理する | Microsoft Docs
description: PIM のセキュリティ アラートについて説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 5d65201c8940e63e3ba514ae0a2dc8a247d7b859
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233514"
---
# <a name="manage-security-alerts-for-azure-resources-by-using-privileged-identity-management"></a>Privileged Identity Management を使用して Azure リソースのロールのセキュリティ アラートを管理する
環境内で疑わしいアクティビティや危険なアクティビティが行われると、Azure リソース向けの Privileged Identity Management (PIM) によりアラートが生成されます。 アラートがトリガーされると、[アラート] ページに表示されます。 

![Alerts page](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>アラートを確認する
アラートを選択して、アラートをトリガーしたユーザーまたはロールの他に解決策のアドバイスを示すレポートを表示します。

![アラート レポート](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>アラート
| アラート: | 重大度 | トリガー | 推奨 |
| --- | --- | --- | --- |
| **リソースに割り当てられている所有者が多すぎます** |Medium |所有者ロールを持っているユーザーが多すぎます。 |リストに表示されているユーザーを見直して、数人のユーザーに低い特権ロールを再割り当てします。 |
| **リソースに割り当てられている永続的な所有者が多すぎます** |Medium |ロールに永続的に割り当てられているユーザーが多すぎます。 |リストに表示されているユーザーを見直して、数人のユーザーにロールを使用するにはアクティブ化が必要であるように再割り当てします。 |
| **重複したロールが作成されました** |Medium |複数のロールが同じ条件を持っています。 |これらのロールのいずれかのみを使用します。 |


### <a name="severity"></a>重大度
* **高**: ポリシー違反のため直ちに対処が必要です。 
* **Medium**:早急の対処は必要ありませんが、ポリシー違反の可能性が通知されています。
* **低**: 早急な対処は必要ありませんが、望ましいポリシー変更が提案されています。

## <a name="configure-security-alert-settings"></a>セキュリティ アラート設定の構成
[アラート] ページから **[設定]** に移動します。
![設定](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

環境で機能し、セキュリティの目標に合うようにさまざまなアラートの設定をカスタマイズします。
![設定をカスタマイズする](media/azure-pim-resource-rbac/rbac-alert-settings.png)

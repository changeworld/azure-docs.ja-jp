---
title: PIM で Azure AD ディレクトリ ロールに対するセキュリティの警告を構成する | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure AD ディレクトリ ロールに対するセキュリティの警告を構成する方法を説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: fc39b6ad2dd63d45995b76011f4ebbe0228b4c2d
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190393"
---
# <a name="configure-security-alerts-for-azure-ad-directory-roles-in-pim"></a>PIM で Azure AD ディレクトリ ロールに対するセキュリティの警告を構成する
## <a name="security-alerts"></a>セキュリティのアラート
環境内で疑わしいアクティビティや危険なアクティビティが行われると、Azure Privileged Identity Management (PIM) によりアラートが生成されます。 アラートは、トリガーされると PIM ダッシュボードに表示されます。 アラートを選択して、アラートをトリガーしたユーザーまたはロールが一覧表示されたレポートを表示します。

![PIM ダッシュボードのセキュリティ アラート - スクリーンショット](./media/pim-how-to-configure-security-alerts/PIM_security_dash.png)

| アラート: | severity | トリガー | 推奨 |
| --- | --- | --- | --- |
| **ロールが PIM の範囲外に割り当てられています** |高 |PIM インターフェイスの外部で、ユーザーに特権ロールが永続的に割り当てられました。 |リスト内のユーザーを確認し、PIM の外部で割り当てられた特権ロールから、これらのユーザーへの割り当てを解除します。 |
| **ロールをアクティブ化する頻度が高すぎます** |Medium |設定で許可された時間に行われた同じロールの再アクティブ化が多すぎます。 |ロールを何度もアクティブ化したユーザーに連絡を取り、理由を確認してください。 時間制限が短すぎてタスクを完了できていない場合や、ロールを自動的にアクティブ化するスクリプトを使用している可能性があります。 このロールのアクティブ化期間として、タスクを実行するのに必要な十分な期間が設定されていることを確認してください。 |
| **Roles don't require multi-factor authentication for activation (ロールのアクティブ化に多要素認証が要求されていません)** |Medium |MFA が有効化されていないロールが設定に存在します。 |最も高い特権を持つロールでは MFA が必須とされていますが、すべてのロールのアクティブ化に対して MFA を有効化することを強くお勧めします。 |
| **ユーザーが特権ロールを使用していません** |低 |最近ロールをアクティブ化していない、資格のある管理者がいます。 |アクセス レビューを開始して、アクセスが不要なユーザーを確認してください。 |
| **グローバル管理者が多すぎます** |低 |推奨されている数よりも多くのグローバル管理者がいます。 |グローバル管理者の数が多い場合、ユーザーに必要以上の権限が与えられている可能性があります。 低い権限のロールにユーザーを移動するか、一部のユーザーを永続的な割り当てではなくロールの候補にしてください。 |

### <a name="severity"></a>severity
* **高**: ポリシー違反のため直ちに対処が必要です。 
* **Medium**:早急の対処は必要ありませんが、ポリシー違反の可能性が通知されています。
* **低**: 早急な対処は必要ありませんが、望ましいポリシー変更が提案されています。

## <a name="configure-security-alert-settings"></a>セキュリティ アラート設定の構成
環境およびセキュリティ上の目標に合わせて、PIM のセキュリティ アラートの一部をカスタマイズすることができます。 次の手順に従って [設定] ブレードに移動します。

1. [Azure Portal](https://portal.azure.com/) にサインインし、ダッシュボードで **[Azure AD Privileged Identity Management]** タイルを選択します。
2. **[特権ロールの管理]** > **[設定]** > **[通知の設定]** の順に選択します。
   
    ![セキュリティ アラートの設定への移動](./media/pim-how-to-configure-security-alerts/PIM_security_settings.png)

### <a name="roles-are-being-activated-too-frequently-alert"></a>"ロールをアクティブ化する頻度が高すぎます" アラート
このアラートは、ユーザーが指定期間内に同じ特権ロールを複数回アクティブ化するとトリガーされます。 期間とアクティブ化の回数の両方を構成できます。

* **アクティブ化の更新の時間枠**: 不審な更新の追跡に使用する期間を日、時間、分、および秒で指定します。
* **Number of activation renewals (アクティブ化の更新回数)**: アラートを行う必要があるとみなす、指定した時間枠内でのアクティブ化の回数を 2 ～ 100 で指定します。 スライダーを動かすか、テキスト ボックスに数字を入力して設定を変更できます。

### <a name="there-are-too-many-global-administrators-alert"></a>"グローバル管理者が多すぎます" アラート
このアラートは 2 つの異なる条件が満たされるとトリガーされます。これらの条件は両方とも構成できます。 1 つ目の条件として、グローバル管理者の数が特定のしきい値に達する必要があります。 2 つ目として、総ロール割り当て数のうち一定の割合がグローバル管理者である必要があります。 これらの測定値の一方のみが満たされている場合は、アラートは表示されません。  

* **Minimum number of Global Administrators**(グローバル管理者の最低人数): 安全ではないとみなすグローバル管理者の人数を、2 ～ 100 で指定します。
* **[グローバル管理者の割合]**: 環境内で安全ではないとする、グローバル管理者である管理者の割合を 0% ～ 100 % で指定します。

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>"管理者が特権ロールを使用してません" アラート
このアラートは、ユーザーがロールをアクティブ化しないまま一定の時間が過ぎるとトリガーされます。

* **日数**: ユーザーがロールをアクティブ化しないままでいられる日数を 0 ～ 100 で指定します。

## <a name="next-steps"></a>次の手順

- [PIM で Azure AD ディレクトリ ロールの設定を構成する](pim-how-to-change-default-settings.md)
- [PIM で Azure AD ディレクトリ ロールに対して多要素認証を要求する](pim-how-to-require-mfa.md)

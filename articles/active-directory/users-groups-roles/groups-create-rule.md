---
title: 動的グループの作成と状態チェックを行う - Azure Active Directory | Microsoft Docs
description: Azure portal でグループ メンバーシップ ルールの作成と状態チェックを行う方法。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/12/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb4f9d2f78857231d0ecd81a2538a75b4b8a2f74
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650280"
---
# <a name="create-a-dynamic-group-and-check-status"></a>動的グループの作成と状態チェックを行う

Azure Active Directory (Azure AD) では、ユーザーまたはデバイスのプロパティに基づいてグループ メンバーシップを決定するルールを使用できます。 この記事では、Azure portal で動的グループのルールを設定する方法について説明します。
セキュリティ グループまたは Office 365 グループに対して、動的メンバーシップがサポートされています。 グループ メンバーシップのルールが適用されるときに、ユーザーとデバイスの属性がメンバーシップのルールと一致するかどうかが評価されます。 ユーザーまたはデバイスの属性が変更されると、組織内のすべての動的グループ ルールが、メンバーシップの変更のために処理されます。 ユーザーとデバイスは、グループの条件を満たす場合に、追加または削除されます。 セキュリティ グループはデバイスとユーザーのどちらにも使用できますが、Office 365 グループはユーザー グループのみが可能です。

構文の例、メンバーシップ ルールでサポートされているプロパティ、演算子、および値については、「[Azure Active Directory の動的グループ メンバーシップ ルール](groups-dynamic-membership.md)」を参照してください。

## <a name="to-create-a-group-membership-rule"></a>グループ メンバーシップ ルールを作成するには

1. 全体管理者、Intune 管理者、またはテナントのユーザー管理者ロールであるアカウントを使用して、[Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
2. **[グループ]** を選びます。
3. **[すべてグループ]** を選び、 **[新しいグループ]** を選びます。

   ![新しいグループを追加するコマンドを選択する](./media/groups-create-rule/new-group-creation.png)

4. **[グループ]** ページで、新しいグループの名前と説明を入力します。 ユーザーまたはデバイスのいずれかに対して **[メンバーシップの種類]** を選択し、 **[動的クエリの追加]** を選択します。 ルール ビルダーでは、最大で 5 つの式がサポートされます。 6 番目以降の式を追加するには、テキスト ボックスを使用する必要があります。

   ![動的グループのメンバーシップのルールを追加する](./media/groups-create-rule/add-dynamic-group-rule.png)

5. メンバーシップのクエリで使用できるカスタム拡張機能プロパティを表示するには、次の手順を実行します。
   1. **[カスタム拡張機能のプロパティを取得します]** を選択します。
   2. アプリケーション ID を入力し、 **[プロパティの更新]** を選択します。
6. ルールを作成した後、 **[保存]** を選択します。
7. **[新規グループ]** ページで **[作成]** をクリックして、グループを作成します。

入力したルールが有効でない場合は、ルールを処理できなかった理由の説明がポータルの Azure 通知に表示されます。 ルールを修正する方法を理解するには、こちらを注意深くお読みください。

## <a name="turn-on-or-off-welcome-email"></a>ウェルカム メールをオンまたはオフにする

新しい Office 365 グループが作成されると、グループに追加されたユーザー宛にウェルカム通知が送信されます。 後で、ユーザーまたはデバイスのいずれかの属性が変更される場合は、組織内のすべての動的グループ ルールが、メンバーシップの変更のために処理されます。 追加されたユーザーは、ウェルカム通知も受け取ります。 この動作は、[Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps) を使用して無効にすることができます。

## <a name="check-processing-status-for-a-rule"></a>ルールの処理状態をチェックする

グループの **[概要]** ページで、メンバーシップの処理状態と最終更新日を確認できます。
  
  ![動的グループの状態の表示](./media/groups-create-rule/group-status.png)

**[メンバーシップの処理]** の状態には、次の状態メッセージが表示される場合があります。

* **評価中**:グループの変更が受信され、更新プログラムが評価されています。
* **処理中**:更新プログラムが処理されています。
* **更新の完了**:処理が完了し、該当するすべての更新が行われました。
* **処理エラー**:メンバーシップのルールを評価するエラーが原因で、処理を完了できませんでした。
* **更新の一時停止**:動的メンバーシップ規則の更新プログラムが管理者によって一時停止されました。 MembershipRuleProcessingState は、"一時停止" に設定されます。

**[メンバーシップの最終更新日時]** の状態には、次の状態メッセージが表示される場合があります。

* &lt;**日付と時刻**&gt;:メンバーシップが最後に更新された日時。
* **進行中**: 更新は現在進行中です。
* **不明**:最終更新時刻を取得することができません。 新しいグループである可能性があります。

特定のグループのメンバーシップ規則の処理中にエラーが発生すると、そのグループの **[概要]** ページの上部にアラートが表示されます。 24 時間以上、テナント内のすべてのグループに対して保留中の動的メンバーシップの更新が処理できない場合は、 **[すべてグループ]** の上部にアラートが表示されます。

![処理エラー メッセージ アラート](./media/groups-create-rule/processing-error.png)

次の記事は、Azure Active Directory のグループに関する追加情報を提供します。

* [既存のグループの表示](../fundamentals/active-directory-groups-view-azure-portal.md)
* [新しいグループの作成とメンバーの追加](../fundamentals/active-directory-groups-create-azure-portal.md)
* [グループの設定の管理](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [グループのメンバーシップの管理](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [グループ内のユーザーの動的ルールの管理](groups-dynamic-membership.md)

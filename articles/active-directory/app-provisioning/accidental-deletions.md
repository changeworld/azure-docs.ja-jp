---
title: Azure Active Directory でのアプリケーションのプロビジョニングで誤った削除の防止機能を有効にする
description: Azure Active Directory でのアプリケーションのプロビジョニングで誤った削除の防止機能を有効にします。
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 09/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: bc100df9a0d666048ac64ae4d496c68a793deabb
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129991909"
---
# <a name="enable-accidental-deletions-prevention-in-the-azure-ad-provisioning-service-preview"></a>Azure AD プロビジョニング サービス (プレビュー) で誤った削除の防止機能を有効にする

Azure AD プロビジョニング サービスには、誤って削除されないようにするための機能が含まれています。 この機能により、予期せずにユーザーがアプリケーションで無効化または削除されないようにすることができます。 

この機能を使用して、削除のしきい値を指定できます。このしきい値を超えると、管理者は削除の処理を進めるために、明示的に選択しなければならなくなります。

> [!NOTE]
> Workday/SuccessFactors 統合では、誤った削除の防止機能はサポートされていません。 また、スコープの変更 (スコープ フィルターの変更、「すべてのユーザーとグループの同期」から「割り当てられたユーザーとグループの同期」への変更など) はサポートされていません。 誤った削除の防止機能が完全にリリースされるまでは、URL (https://aka.ms/AccidentalDeletionsPreview ) を使用して Azure portal にアクセスする必要があります。


## <a name="configure-accidental-deletion-prevention"></a>誤った削除の防止機能を構成する
誤った削除の防止機能を有効にするには:
1.  Azure Portal で、 **[Azure Active Directory]** を選びます。
2.  **[エンタープライズ アプリケーション]** を選択してから、ご使用のアプリを選択します。
3.  **[プロビジョニング]** を選択し、プロビジョニング ページで **[Edit provisioning (プロビジョニングの編集)]** を選択します。
4. **[設定]** で、 **[Prevent accidental deletions (誤削除の防止)]** チェックボックスをオンにして、削除のしきい値を指定します。 また、通知用の電子メール アドレスが指定されていることを確認してください。 削除のしきい値に達すると、電子メールが送信されます。
5. **[保存]** を選択して変更を保存します。

削除のしきい値に達すると、ジョブは検疫に移動され、通知の電子メールが送信されます。 その後、検疫されたジョブを許可または拒否できます。 検疫状態の詳細については、「[検疫状態のアプリケーションのプロビジョニング](application-provisioning-quarantine-status.md)」をご覧ください。

## <a name="known-limitations"></a>既知の制限事項
次の2つの重要な制限事項に注意して、積極的に対処する必要があります。
- Workday と SuccessFactors からの HR 主導のプロビジョニングでは、誤った削除の防止機能はサポートされていません。 
- プロビジョニング構成への変更 (スコープの変更など) は、誤った削除の防止機能ではサポートされません。 

## <a name="recovering-from-an-accidental-deletion"></a>誤って削除されたものからの復旧
誤って削除してしまった場合、[プロビジョニングの状態] ページにその内容が表示されます。  **[Provisioning has been quarantined. See quarantine details for more information. (プロビジョニングが検疫されました。詳細については、検疫の詳細を参照してください。)]** というメッセージが表示されます。

**[Allow deletes (削除を許可する)]** または **[プロビジョニング ログの表示]** のいずれかを選択できます。

### <a name="allowing-deletions"></a>削除を許可する

**[Allow deletes]\(削除を許可する\)** アクションを使用して、誤削除のしきい値をトリガーしたオブジェクトを削除します。  削除を受け入れるには、次の手順に従います。  

1. **[Allow deletes (削除を許可する)]** を選択します。
2. 確認の **[はい]** をクリックして削除を許可します。
3. 削除が受け入れられたことを確認するメッセージが表示され、次のサイクルで状態が正常に戻ります。

### <a name="rejecting-deletions"></a>削除の拒否

削除を許可しない場合は、次の操作を行う必要があります。
- 削除のソースを調査します。 詳細については、プロビジョニング ログで確認できます。
- ユーザー/グループを再びアプリに割り当てるか、ユーザー/グループを復元するか、プロビジョニングの構成を更新して、削除を防止します。
- ユーザー/グループが削除されないように、必要な変更を加えたら、プロビジョニングを再開します。 ユーザーまたはグループが削除されないようにするために必要な変更を行うまで、プロビジョニングを再開しないでください。 


### <a name="test-deletion-prevention"></a>削除防止機能をテストする
この機能をテストするには、disable/delete イベントをトリガーします。たとえば、しきい値を 3 のような小さい値に設定し、スコープ フィルターを変更してユーザーを割り当て解除し、ディレクトリからユーザーを削除します (次のセクションの一般的なシナリオを参照してください)。 

プロビジョニング ジョブを実行して (20～40 分)、[プロビジョニング] ページに戻ります。 検疫中のプロビジョニング ジョブが表示されます。削除を許可するか、プロビジョニング ログを確認して、削除が発生した理由を把握できます。

## <a name="common-de-provisioning-scenarios-to-test"></a>テストに使用する一般的なプロビジョニング解除シナリオ
- ユーザーを削除するか、ごみ箱に入れます。
- ユーザーのサインインをブロックします。
- アプリケーションからユーザーまたはグループを割り当て解除します。
- アプリへのアクセスを提供しているグループからユーザーを削除します。

プロビジョニング解除のシナリオの詳細については、「[アプリケーションのプロビジョニングのしくみ](how-provisioning-works.md#de-provisioning)」を参照してください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="what-scenarios-count-toward-the-deletion-threshold"></a>削除のしきい値に対してどのようなシナリオがカウントされますか。
対象のアプリケーションからユーザーが削除されるように設定されると、削除のしきい値に対してカウントされます。 対象のアプリケーションからユーザーが削除される可能性のあるシナリオには、アプリケーションからユーザーの割り当てを解除する、ディレクトリ内のユーザーをソフト/ハード削除するなどがあります。 削除対象として評価されたグループは、削除のしきい値にカウントされます。 削除に加えて、無効化についても同じように機能します。

### <a name="what-is-the-interval-that-the-deletion-threshold-is-evaluated-on"></a>削除のしきい値が評価される間隔はどのくらいですか。
サイクルごとに評価されます。 1 回のサイクルで削除の回数がしきい値を超えない場合、「サーキットブレーカー」はトリガーされません。 定常状態にするために複数のサイクルが必要な場合、削除のしきい値はサイクルごとに評価されます。

### <a name="how-are-these-deletion-events-logged"></a>これらの削除イベントはどのようにログに記録されますか。
無効化または削除する必要があるが、削除のしきい値が原因で、それが行われていないユーザーが見つかることがあります。 **[プロビジョニング ログ]** に移動し、[*StagedAction*] または [*StagedDelete*] を指定して **[アクション]** をフィルターに掛けます。


## <a name="next-steps"></a>次のステップ 

- [アプリケーションのプロビジョニングのしくみ](how-provisioning-works.md)
- [アプリケーションのプロビジョニングのデプロイ計画](plan-auto-user-provisioning.md)

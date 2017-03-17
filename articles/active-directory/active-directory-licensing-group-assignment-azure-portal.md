---

title: "Azure Active Directory でのグループへのライセンス割り当て | Microsoft Docs"
description: "Azure Active Directory のグループベースのライセンスを使用してライセンスを割り当てる方法"
services: active-directory
keywords: "Azure AD のライセンス"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: f8b63e5831897d3a45298b0415bb2d6d44ab0de1
ms.openlocfilehash: 73727d8d1e5de01794589538bd67d3d698d325a0
ms.lasthandoff: 03/01/2017


---

# <a name="assign-licenses-to-a-group-of-users-in-azure-active-directory"></a>Azure Active Directory でのユーザーのグループへのライセンスの割り当て

この記事では、製品ライセンスをグループに割り当てて、グループのメンバー全員に正しくライセンスを付与できたことを確認するという、基本のシナリオについて説明します。

この例では、人事部門の全メンバーが含まれている、**人事部**という名前のセキュリティ グループがテナントに含まれており、そのグループのユーザーの数は約 1,000 人となっています。 管理者は、Office 365 Enterprise E3 ライセンスを部署全体に割り当てる必要があります。また、この製品に含まれた Yammer Enterprise サービスを、部署での使用の準備が整うまで、一時的に無効にしておく必要があります。 管理者は、Enterprise Mobility + Security ライセンスを同じユーザー グループにデプロイする必要もあります。

## <a name="step-1-assign-the-required-licenses"></a>手順 1. 必要なライセンスの割り当て

1. 管理者アカウントを使用して、[**Azure Portal**](https://portal.azure.com) にサインインします。 ライセンスを管理するには、全体管理者ロールかユーザー アカウント管理者ロールがアカウントに必要です。

2. 左側のナビゲーション ウィンドウで **[その他のサービス]** を選択し、**[Azure Active Directory]** を選択します。 星のアイコンをクリックしてこのブレードを "お気に入り" にすることができます。また、ポータルのダッシュボードへのピン留めも可能です。

3. **[Azure Active Directory]** ブレードの **[ライセンス]** を選択します。 この操作で開かれるブレードで、テナント内でライセンスを付与できる全製品を確認して管理できます。

4. **[すべての製品]** で、製品名を選択して Office 365 Enterprise E3 と Enterprise Mobility + Security の両方を選択します。 ブレード上部の **[割り当て]** を選択して、割り当てを開始します。

  ![すべての製品、ライセンスの割り当て](media/active-directory-licensing-group-assignment-azure-portal/all-products-assign.png)

5. **[ライセンスの割り当て]** ブレードで **[ユーザーとグループ]** をクリックして、ユーザーとグループのブレードを開きます。 "*人事部*" というグループ名を探してグループを選択し、ブレードの下部にある **[選択]** をクリックして忘れずに確定します。

  ![グループの選択](media/active-directory-licensing-group-assignment-azure-portal/select-a-group.png)

6. **[ライセンスの割り当て]** ブレードで **[割り当てオプション (省略可能)]** をクリックすると、先ほど選択した&2; つの製品に含まれているすべてのサービス プランが表示されます。 Yammer Enterprise を探して **[オフ]** に設定し、製品ライセンスのこのサービスを無効にします。 **[Assignment options (割り当てオプション)]** の下部にある **[OK]** をクリックして確定します。

  ![割り当てオプション](media/active-directory-licensing-group-assignment-azure-portal/assignment-options.png)

7. 最後に、**[ライセンスの割り当て]** ブレードの下部にある **[割り当て]** をクリックして、割り当てを完了します。

8. 右上隅に通知が表示され、プロセスの状態と結果が示されます。 (たとえば、グループ内の既存のライセンスが原因で) グループへの割り当てを完了できなかった場合は、通知をクリックして失敗の詳細を確認します。

これで、人事部グループへのライセンス テンプレートの指定が完了しました。 開始された Azure AD のバックグラウンド プロセスによって、このグループの既存のメンバー全員が処理されます。 グループの現在の規模によっては、この初期操作に時間がかかる場合があります。 次の手順では、プロセスの完了を確認する方法と、問題の解決にさらなる注意が必要かどうかを確認する方法について説明します。

> [!NOTE]
> Azure AD の **[ユーザーとグループ]** からでも同様に割り当てを開始できます。 **[Azure Active Directory] &gt; [ユーザーとグループ] &gt; [すべてのグループ]** の順に移動し、グループを探して選択します。次に、**[ライセンス]** タブに移動します。 ブレードの上部にある **[割り当て]** ボタンをクリックすると、ライセンス割り当てのブレードが開きます。

## <a name="step-2-verify-that-the-initial-assignment-has-completed"></a>手順 2. 最初の割り当てが完了したことの確認

1. **[Azure Active Directory] &gt; [ユーザーとグループ] &gt; [すべてのグループ]** の順に移動して、ライセンスを割り当てた "*人事部*" グループを見つけます。

2. "*人事部*" グループのブレードで **[ライセンス]** を選択して、ライセンスがユーザーに完全に割り当てられていることと、調査が必要なエラーの有無をすばやく確認します。この内容には次が含まれます。

  - グループに割り当て済みの製品ライセンス。 エントリを選択すると、有効にした特定のサービスを表示して変更を行えます。

  - ライセンスの割り当てに施された最新の変更の状態 (変更が処理中の場合、またはすべてのユーザー メンバーの処理が完了している場合)。

  - エラー状態のユーザーの情報 (エラーがあった場合)。これらのユーザーにライセンスは割り当てられませんでした。

  ![割り当てオプション](media/active-directory-licensing-group-assignment-azure-portal/assignment-errors.png)

3. ライセンスの処理に関する詳細情報は、**[Azure Active Directory] &gt; [ユーザーとグループ] &gt;
   " *グループ名*" &gt; [監査ログ]** の順に移動して入手できます。

  - アクティビティ: **ユーザーに対するグループ ベースのライセンスの適用を開始する**。 システムによってグループでのライセンス割り当ての変更が検出され、すべてのユーザー メンバーに対するその変更の適用が開始されたときに、ログが記録されます。 ここには、行われた変更の情報が含まれます。

  - アクティビティ: **ユーザーに対するグループ ベースのライセンスの適用を終了する**。 システムによるグループ内のユーザー全員の処理が完了したときに、ログが記録されます。 ここには、処理が正常に完了したユーザーの数とグループ ライセンスを割り当てることができなかったユーザーの数の概要が含まれます。

## <a name="step-3-checking-for-license-problems-and-resolving-them"></a>手順 3. ライセンスに関する問題のチェックと解決

1. **[Azure Active Directory] &gt; [ユーザーとグループ] &gt; [すべてのグループ]** の順に移動して、ライセンスを割り当てた "*人事部*" グループを見つけます。

2. **人事部**グループのブレードで **[ライセンス]** を選択します。 ブレードの上部の通知で、ライセンスを割り当てられなかったユーザーが 10 人いることが示されます。 ここで開かれるリストに、このグループでライセンスがエラー状態である全ユーザーが一覧表示されます。

3. **[割り当ての失敗数]** 列から、どちらの製品ライセンスもユーザーに割り当てられなかったことがわかります。 **[エラーの最も大きな原因]** にはエラーの原因が記載されています。この例では **[競合するサービス プラン]** です。

  ![失敗した割り当て](media/active-directory-licensing-group-assignment-azure-portal/failed-assignments.png)

4. ユーザーを選択して **[ライセンス]** ブレードを開き、そのユーザーに現在割り当てられているすべてのライセンスを表示します。 この例では、このユーザーは**キオスク ユーザー** グループから Office 365 Enterprise E1 のライセンスを継承済みであることがわかります。 この E1 ライセンスは、システムによって適用される**人事部**グループの E3 ライセンスと競合します。そのため、このグループのライセンスは&1; つもこのユーザーに割り当てられませんでした。

  ![ユーザーのライセンスの表示](media/active-directory-licensing-group-assignment-azure-portal/user-license-view.png)

5. この問題を解決するには、**キオスク ユーザー** グループからこのユーザーを削除します。 Azure AD によって変更が処理された後、**人事部**のライセンスは正常に割り当てられます。

  ![正常に割り当てられたライセンス](media/active-directory-licensing-group-assignment-azure-portal/license-correctly-assigned.png)

## <a name="next-steps"></a>次のステップ

グループを使用したライセンス管理の機能セットについては、以下をご覧ください。

* [Azure Active Directory のグループベースのライセンスとは](active-directory-licensing-whatis-azure-portal.md)
* [Azure Active Directory のグループのライセンスに関する問題の特定と解決](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Azure Active Directory で個別にライセンスを付与されたユーザーをグループベースのライセンスに移行する方法](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory グループベース ライセンスのその他のシナリオ](active-directory-licensing-group-advanced.md)


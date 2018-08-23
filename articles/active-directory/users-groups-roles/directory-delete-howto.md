---
title: Azure Active Directory テナント ディレクトリを削除する | Microsoft Docs
description: 削除用の Azure AD テナント ディレクトリを準備する方法について説明します
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 06/13/2018
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: b1d3439412e324c71687c43aa9e47c520cb72262
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42144482"
---
# <a name="delete-an-azure-active-directory-tenant"></a>Azure Active Directory テナントの削除
テナントを削除すると、そのテナントに含まれるリソースもすべて削除されます。 テナントを削除する前の準備として、その関連リソースを最小限にする必要があります。 ポータルから Azure AD テナントを削除できるのは、Azure Active Directory (Azure AD) グローバル管理者だけです。

## <a name="prepare-the-tenant-for-deletion"></a>削除のためにテナントを準備する

いくつかのチェックに合格するまで、Azure AD テナントを削除することはできません。 これらのチェックにより、テナントの削除がユーザーのアクセスに悪影響を及ぼす (Office 365 にサインインできない、Azure のリソースにアクセスできないなど) リスクが軽減されます。 たとえば、サブスクリプションに関連付けられたテナントが誤って削除された場合、ユーザーはそのサブスクリプションの Azure リソースにアクセスできなくなります。 次の例で、チェックする条件を説明します。

* テナントを削除するグローバル管理者 1 人を除き、ユーザーがテナント内に存在しないこと。 テナントを削除するには、他のすべてのユーザーを削除しておく必要があります。 ユーザーがオンプレミスから同期されている場合は、同期を無効にする必要があります。また、Azure Portal または Azure PowerShell のコマンドレットを使用して、クラウド テナントでユーザーを削除する必要があります。 
* テナント内にアプリケーションが存在しないこと。 テナントを削除するには、すべてのアプリケーションを削除しておく必要があります。
* テナントにリンクされる Multi-Factor Authentication プロバイダーが存在しないこと。
* テナントに関連付けられている、Microsoft Online Services のサブスクリプション (Microsoft Azure、Office 365、Azure AD Premium など) が存在しないこと。 たとえば、Azure で既定のテナントが作成されている場合、Azure サブスクリプションが認証にこのテナントを引き続き使用していれば、このテナントを削除することはできません。 同様に、別のユーザーがテナントにサブスクリプションを関連付けている場合、そのテナントを削除することはできません。 

## <a name="delete-an-azure-ad-tenant"></a>Azure AD テナントを削除する

1. テナントのグローバル管理者のアカウントを使って、[Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

2. **[Azure Active Directory]** を選択します。

3. 削除するテナントに切り替えます。
  
  ![[ディレクトリの削除] ボタン](./media/directory-delete-howto/delete-directory-command.png)

4. **[ディレクトリの削除]** を選択します。
  
  ![[ディレクトリの削除] ボタン](./media/directory-delete-howto/delete-directory-list.png)

5. テナントがチェックで 1 つ以上不合格になった場合、合格する方法に関する詳細情報へのリンクが提供されます。 すべてのチェックに合格したら、**[削除]** を選択してプロセスを完了します。

## <a name="i-have-an-expired-subscription-but-i-cant-delete-the-tenant"></a>サブスクリプションが期限切れになったが、テナントを削除できない

Azure Active Directory テナントを構成したときに、Azure Active Directory Premium P2、Office 365 Business Premium、または Enterprise Mobility + Security E5 など、組織のライセンス ベースのサブスクリプションもアクティブ化している可能性があります。 偶発的なデータ損失を回避するため、これらのサブスクリプションを完全に削除するまで、ディレクトリの削除がブロックされます。 テナントを削除するには、サブスクリプションが**プロビジョニング解除済み**状態である必要があります。 **有効期限切れ**または**キャンセル済み**のサブスクリプションは、**無効**状態に移行します。最終段階が**プロビジョニング解除済み**状態です。 

試用版の Office 365 サブスクリプション (有償のパートナー/CSP、Enterprise Agreement、ボリューム ライセンスは含まれません) の有効期限が切れたときに起こることについては、次の表を参照してください。 Office 365 のデータ保有とサブスクリプションのライフ サイクルの詳細については、「[一般法人向け Office 365 のサブスクリプションが終了したとき、データとアクセスはどうなりますか?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3)」を参照してください。 

サブスクリプションの状態 | データ | データへのアクセス
----- | ----- | -----
アクティブ (試用版の 30 日間)  | 誰もがアクセスできるデータ    | <li>ユーザーは Office 365 のファイルやアプリへの通常のアクセス権を持ちます<li>管理者は Office 365 管理センターとリソースへの通常のアクセス権を持ちます 
有効期限切れ (30 日間)   | 誰もがアクセスできるデータ    | <li>ユーザーは Office 365 のファイルやアプリへの通常のアクセス権を持ちます<li>管理者は Office 365 管理センターとリソースへの通常のアクセス権を持ちます
無効 (30 日間) | 管理者だけがアクセスできるデータ  | <li>ユーザーは Office 365 のファイルやアプリにアクセスできません<li>管理者は Office 365 管理センターにアクセスできますが、ライセンスを割り当てたり、ユーザーを更新したりすることはできません
プロビジョニング解除 (無効後 30 日間) | 削除されたデータ (使用中の他のサービスがない場合は自動的に削除) | <li>ユーザーは Office 365 のファイルやアプリにアクセスできません<li>管理者は Office 365 管理センターにアクセスして他のサブスクリプションの購入および管理ができます 

ビジネス向け Microsoft Store の管理センターを使用して、サブスクリプションを**プロビジョニング解除**状態にして、3 日で削除されるようにすることができます。 この機能は、Office 365 管理センターでまもなく公開されます。

1. テナントのグローバル管理者のアカウントを使用して、[ビジネス向け Microsoft Store の管理センター](https://businessstore.microsoft.com/manage/)にサインインします。 既定の初期ドメイン contoso.onmicrosoft.com を持つ "Contoso" テナントを削除する場合は、admin@contoso.onmicrosoft.com などの UPN 使用してサインオンします。

2. **[管理]** タブに移動して、**[製品およびサービス]** を選択し、キャンセルするサブスクリプションを選択します。 **[キャンセル]** クリックすると、ページが更新されます。
  
  ![サブスクリプションを削除するための [削除] リンク](./media/directory-delete-howto/delete-command.png)
  
3. **[削除]** を選択してサブスクリプションを削除し、使用条件に同意します。 すべてのデータは 3 日以内に完全に削除されます。 気が変わった場合は、3 日の間にサブスクリプションを再アクティブ化することができます。
  
  ![使用条件](./media/directory-delete-howto/delete-terms.png)

4. サブスクリプションの状態が変更され、サブスクリプションが削除対象としてマークされています。 このサブスクリプションは、72 時間後に**プロビジョニング解除済み**状態になります。

5. テナントのサブスクリプションを削除して 72 時間が経過すると、Azure AD 管理センターに再度サインインすることができます。何もしなくても、テナントの削除をブロックしているサブスクリプションはなくなっているはずです。 Azure AD テナントを正常に削除できるはずです。
  
  ![削除画面でサブスクリプションのチェックに合格](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="next-steps"></a>次の手順
[Azure Active Directory のドキュメント](https://docs.microsoft.com/azure/active-directory/)

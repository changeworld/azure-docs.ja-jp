---
title: Azure 管理者サブスクリプション ロールを追加または変更する | Microsoft Docs
description: Azure 共同管理者、サービス管理者、アカウント管理者を追加または変更する方法について説明します
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: genli
ms.openlocfilehash: 97210c0d9dba9c4130b1da9ad17a257ff1d81b42
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448971"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Azure サブスクリプション管理者を追加または変更する

Azure リソースへのアクセスを管理するには、適切な管理者ロールが必要です。 この記事では、サブスクリプション レベルでユーザーの管理者ロールを追加または変更する方法について説明します。

## <a name="what-administrator-role-do-i-use"></a>どの管理者ロールを使用しますか?

Azure にはさまざまなロールが用意されています。 リソースへのアクセスを管理するには、サービス管理者、共同管理者などの従来のサブスクリプション管理者ロールか、ロールベースのアクセス制御 (RBAC) と呼ばれる新しい認可システムを使用できます。 アクセス管理を確実かつ適切に制御し、簡略化するには、すべてのアクセス管理のニーズに対応する RBAC を使用することをお勧めします。 可能な場合は、RBAC を使用して既存のアクセス ポリシーを再構成することをお勧めします。 詳細については、[ロールベースのアクセス制御 (RBAC) の概要](../role-based-access-control/overview.md)に関するページおよび [Azure の各種ロールの説明](../role-based-access-control/rbac-and-directory-admin-roles.md)に関するページを参照してください。

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-for-a-subscription-in-azure-portal"></a>Azure portal でサブスクリプションの RBAC 所有者を追加する 

Azure サブスクリプションの管理者としてユーザーを追加するには、サブスクリプション スコープで、そのユーザーに[所有者](../role-based-access-control/built-in-roles.md#owner)ロール (RBAC ロール) を割り当てます。 所有者ロールでは、管理者が割り当て、他のサブスクリプションにアクセス権がないサブスクリプションのリソースを管理できます。

1. [Azure Portal の**サブスクリプション**](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にアクセスします。
2. アクセス権を付与するサブスクリプションを選択します。
3. **[追加]** を選択します。
   ([追加] ボタンがない場合は、アクセス許可を追加する権限がありません。)
4. リストから **[アクセス制御 (IAM)]** を選択します。
5. **[ロール]** ボックスで、**[所有者]** を選択します。 
6. **[アクセスの割り当て先]** ボックスで、**[Azure AD のユーザー、グループ、またはアプリケーション]** を選択します。 
7. **[選択]** ボックスに、所有者として追加するユーザーのメール アドレスを入力します。 ユーザーを選択し、**[保存]** を選択します。

    ![選択された所有者ロールを示すスクリーンショット](./media/billing-add-change-azure-subscription-administrator/add-role.png)

これにより、他のユーザーへアクセス権を委任する権限を含め、すべてのリソースへのフル アクセスがユーザーに付与されます。 リソース グループなど、異なるスコープでアクセスを付与するには、そのスコープの **[アクセス制御 (IAM)]** ブレードにアクセスしてください。

## <a name="add-or-change-co-administrator"></a>共同管理者を追加または変更する

共同管理者として追加できるのは[所有者](../role-based-access-control/built-in-roles.md#owner)のみです。 [共同作成者](../role-based-access-control/built-in-roles.md#contributor)や[閲覧者](../role-based-access-control/built-in-roles.md#reader)などのロールの他のユーザーは、共同管理者として追加できません。

> [!TIP]
> ユーザーが Azure クラシック デプロイを管理する必要がある場合は、所有者を共同管理者として追加するだけです。 他の目的についてはすべて、RBAC を使用することをお勧めします。

1. 既に実行していない場合は、上記の手順に従って、所有者として他のユーザーを追加します。
2. 追加した所有者ユーザーを**右クリック**し、**[共同管理者として追加]** を選択します。 **[共同管理者として追加]** オプションが表示されない場合は、ページを更新するか、別のインターネット ブラウザーを試してください。 

    ![共同管理者追加のスクリーンショット](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    共同管理者のアクセス許可を削除するには、共同管理者であるユーザーを**右クリック**し、**[共同管理者を削除する]** を選択します。

    ![共同管理者削除のスクリーンショット](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Azure サブスクリプションのサービス管理者を変更する

サブスクリプションのサービス管理者を変更できるのは、アカウント管理者のみです。 サインアップ時の既定設定では、サービス管理者はアカウント管理者でもあります。 サービス管理者が別のユーザーに変更されると、アカウント管理者は、Azure portal にアクセスできなくなります。 ただし、アカウント管理者はいつでもアカウント センターを使用して、サービス管理者を自身に戻すことができます。

1. [サービス管理者を変更するための制限事項](#limits)を確認して、使用するシナリオがサポートされていることを確認してください。
1. アカウント管理者として[アカウント センター](https://account.windowsazure.com/subscriptions)にサインインします。
1. サブスクリプションを選択します。
1. 右側にある **[サブスクリプション詳細の編集]** を選択します。

    ![アカウント センターの [サブスクリプションの編集] ボタンを示すスクリーン ショット](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. **[サービス管理者]** ボックスに、新しいサービス管理者のメール アドレスを入力します。

    ![サービス管理者の電子メールを変更する、ボックスを示すスクリーン ショット](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>サービス管理者を変更するための制限事項

* すべてのサブスクリプションは Azure AD ディレクトリと関連付けられています。 サブスクリプションが関連付けられているディレクトリを検索するには、[**サブスクリプション**](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)に移動し、サブスクリプションを選択して、ディレクトリを表示します。
* 職場または学校アカウントを使用してサインインした場合、組織内の他のアカウントをサービス管理者として追加することができます。 たとえば、abby@contoso.com はサービス管理者として bob@contoso.com を追加できますが、john@notcontoso.com が contoso.com ディレクトリ内にない限り john@notcontoso.com は追加できません。 職場または学校アカウントでサインインしているユーザーは、引き続き Microsoft アカウント ユーザーをサービス管理者として追加できます。

  | サインイン方法 | サービス管理者として Microsoft アカウント ユーザーを追加できる | 同じ組織内の職場または学校アカウントをサービス管理者として追加できる | 別の組織内の職場または学校アカウントをサービス管理者として追加できる |
  | --- | --- | --- | --- |
  |  Microsoft アカウント |[はい] |いいえ  |いいえ  |
  |  職場または学校のアカウント |[はい] |[はい] |いいえ  |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Azure サブスクリプションのアカウント管理者を変更する

アカウント管理者とは、Azure サブスクリプションに最初にサインアップしたユーザーで、サブスクリプションの請求先の所有者としての責任を負います。 サブスクリプションのアカウント管理者を変更するには、「[Azure サブスクリプションの所有権を別のアカウントに譲渡する](billing-subscription-transfer.md)」を参照してください。

<a name="check-the-account-administrator-of-the-subscription"></a>

**だれがアカウント管理者かがわからない場合は**、 次の手順に従います。

1. [Azure Portal の**サブスクリプション**](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にアクセスします。
1. 確認するサブスクリプションを選択し、**[設定]** を調べます。
1. **[プロパティ]** を選択します。 サブスクリプションのアカウント管理者が、**[アカウント管理者]** ボックスに表示されます。  

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>リソースのアクセス制御と Active Directory の詳細

* RBAC の詳細については、「[ロールベースのアクセス制御 (RBAC) とは](../role-based-access-control/overview.md)」を参照してください。
* Azure のすべてのロールの詳細については、[Azure の各種ロールの説明](../role-based-access-control/rbac-and-directory-admin-roles.md)に関するページを参照してください。
* Azure Active Directory について詳しくは、「[Azure サブスクリプションを Azure Active Directory に関連付ける方法](../active-directory/active-directory-how-subscriptions-associated-directory.md)」および「[Azure Active Directory での管理者ロールの割り当て](../active-directory/users-groups-roles/directory-assign-admin-roles.md)」をご覧ください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

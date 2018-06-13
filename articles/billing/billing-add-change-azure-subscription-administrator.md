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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 09c2badf6116dd36add6cccc82486d7f5b8f8697
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2018
ms.locfileid: "34069149"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Azure サブスクリプション管理者を追加または変更する

従来の Azure サブスクリプション管理と Azure [ロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) が、Azure リソースへのアクセスを管理する 2 つのシステムです。

* 従来のサブスクリプション管理者ロールは、基本的なアクセス管理を提供しており、アカウント管理者、サービス管理者、および共同管理者が含まれます。
    * 新しい Azure サブスクリプションにサインアップすると、既定では、アカウント管理者およびサービス管理者の両方としてアカウントが設定されます。
    * 共同管理者はサインアップした後に追加できます。
* RBAC はさらに新しいシステムで、多数の組み込みロール、スコープの柔軟性、およびカスタム ロールによってきめ細かなアクセス管理を提供します。
    * ただし、従来のサブスクリプション管理者ロールがなく、RBAC ロールだけを持つユーザーが、Azure クラシック デプロイメントを管理することはできません。

アクセス管理を確実かつ適切に制御し、簡略化するには、すべてのアクセス管理のニーズに対応する RBAC を使用することをお勧めします。 可能な場合は、RBAC を使用して既存のアクセス ポリシーを再構成することをお勧めします。 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>Azure ポータルでサブスクリプションの所有者の RBAC 管理者を追加します。 

Azure サブスクリプション サービス管理の管理者としてユーザーを追加するには、そのユーザーに、サブスクリプションの RBAC 所有者ロールを付与します。 所有者ロールでは、管理者が割り当て、他のサブスクリプションにアクセス権がないサブスクリプションのリソースを管理できます。

1. [Azure Portal の**サブスクリプション**](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にアクセスします。
2. アクセス権を付与するサブスクリプションを選択します。
3. **[追加]** を選択します。  
   ([追加] ボタンがない場合は、アクセス許可を追加する権限がありません。)
4. メニューから **[アクセス制御 (IAM)]** を選択します。
5. **[ロール]** ボックスで、**[所有者]** を選択します。 
6. **[アクセスの割り当て先]** ボックスで、**[Azure AD のユーザー、グループ、またはアプリケーション]** を選択します。 
7. **[選択]** ボックスに、所有者として追加するユーザーのメール アドレスを入力します。 ユーザーを選択し、**[保存]** を選択します。

    ![選択された所有者ロールを示すスクリーンショット](./media/billing-add-change-azure-subscription-administrator/add-role.png)

これにより、他のユーザーへアクセス権を委任する権限を含め、すべてのリソースへのフル アクセスがユーザーに付与されます。 リソース グループなど、異なるスコープでアクセスを付与するには、そのスコープの IAM メニューにアクセスしてください。 

## <a name="add-or-change-co-administrator"></a>共同管理者を追加または変更する

共同管理者として追加できるのは所有者のみです。 共同作成者や閲覧者などのロールの他のユーザーは、共同管理者として追加できません。

> [!TIP]
> ユーザーが Azure クラシック デプロイメントを管理する必要がある場合は、"所有者" アカウントを共同管理者として追加するだけです。 他の目的についてはすべて、RBAC を使用することをお勧めします。

1. 既に実行していない場合は、上記の手順に従って、所有者として他のユーザーを追加します。
2. 追加した所有者ユーザーを**右クリック**し、**[共同管理者として追加]** を選択します。 **[共同管理者として追加]** オプションが表示されない場合は、ページを更新するか、別のインターネット ブラウザーを試してください。 

    ![共同管理者追加のスクリーンショット](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    共同管理者のアクセス許可を削除するには、"共同管理者" を**右クリック**し、**[共同管理者を削除する]** を選択します。

    ![共同管理者削除のスクリーンショット](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Azure サブスクリプションのサービス管理者を変更する

サブスクリプションのサービス管理者を変更できるのは、アカウント管理者のみです。 サインアップ時の既定設定では、サービス管理者はアカウント管理者でもあります。 サービス管理者が別のユーザーに変更されると、アカウント管理者は、Azure Portal にアクセスできなくなります。 ただし、アカウント管理者はいつでもアカウント センターを使用して、サービス管理者を自身に戻すことができます。

1. 「[サービス管理者を変更するための制限事項](#limits)」を参照して、使用するシナリオがサポートされていることを確認してください。
1. アカウント管理者として [Azure センター](https://account.windowsazure.com/subscriptions)にサインインします。
1. サブスクリプションを選択します。
1. 右側にある **[サブスクリプション詳細の編集]** を選択します。

    ![アカウント センターの [サブスクリプションの編集] ボタンを示すスクリーン ショット](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. **[サービス管理者]** ボックスに、新しいサービス管理者の電子メール アドレスを入力します。

    ![サービス管理者の電子メールを変更する、ボックスを示すスクリーン ショット](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>サービス管理者を変更するための制限事項

* すべてのサブスクリプションは Azure AD ディレクトリと関連付けられています。 サブスクリプションが関連付けられているディレクトリを検索するには、[**サブスクリプション**](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)に移動し、サブスクリプションを選択して、ディレクトリを表示します。
* 職場または学校アカウントを使用してサインインした場合、組織内の他のアカウントをサービス管理者として追加することができます。 たとえば、abby@contoso.com はサービス管理者として bob@contoso.com を追加できますが、john@notcontoso.com が contoso.com ディレクトリ内にない限り john@notcontoso.com は追加できません。 職場または学校アカウントでサインインしているユーザーは、Microsoft アカウント ユーザーをサービス管理者として引き続き追加できます。

  | サインイン方法 | SA として Microsoft アカウント ユーザーを追加しますか。 | SA と同じ組織内に、職場または学校のアカウントを追加しますか。 | SA と異なる組織内に、職場または学校のアカウントを追加しますか。 |
  | --- | --- | --- | --- |
  |  Microsoft アカウント |[はい] |いいえ  |いいえ  |
  |  職場または学校のアカウント |[はい] |[はい] |いいえ  |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Azure サブスクリプションのアカウント管理者を変更する

アカウント管理者とは、Azure サブスクリプションに最初にサインアップしたユーザーで、サブスクリプションの請求先の所有者としての責任を負います。 サブスクリプションのアカウント管理者を変更する方法の詳細については、「[Azure サブスクリプションの所有権を別のアカウントに譲渡する](billing-subscription-transfer.md)」を参照してください。

<a name="check-the-account-administrator-of-the-subscription"></a>

「**サブスクリプションのアカウント管理者を確認する**」 次の手順に従います。

1. [Azure Portal の**サブスクリプション**](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にアクセスします。
1. 確認するサブスクリプションを選択し、**[設定]** を調べます。
1. **[プロパティ]** を選択します。 サブスクリプションのアカウント管理者が、**[アカウント管理者]** ボックスに表示されます。  

## <a name="types-of-classic-subscription-admins"></a>従来のサブスクリプション管理者の種類

 Azure の従来のサブスクリプション管理者ロールには、アカウント管理者、サービス管理者、共同管理者の 3 種類があります。 Azure へのサインアップに使用されたアカウントは、自動的にアカウント管理者とサービス管理者の両方として設定されます。 その後、共同管理者を追加できます。 次の表では、この 3 つの管理ロールの厳密な違いについて説明します。 

> [!TIP]
> 制御を強化し、きめ細かなアクセス管理を実現するために、Azure ロールベースのアクセス制御 (RBAC) を使用することをお勧めします。これにより、ユーザーを複数のロールに追加することができます。 詳細については、[Azure Active Directory のロールベースのアクセス制御](../role-based-access-control/overview.md)に関するページをご覧ください。

| 従来のサブスクリプション管理者 | 制限 | [説明] |
| --- | --- | --- |
| アカウント管理者 (AA) |1 Azure アカウントに 1 人 |Azure サブスクリプションにサインアップしたユーザーです。[アカウント センター](https://account.azure.com/Subscriptions)にアクセスし、さまざまな管理タスクを実行する権限が付与されています。 管理タスクには、新しいサブスクリプションの作成、サブスクリプションの取り消し、サブスクリプションの料金の変更、サービス管理者の変更などがあります。 概念的には、アカウント管理者は、サブスクリプションの請求先の所有者です。 RBAC で、アカウント管理者にロールが割り当てられることはありません。|
| サービス管理者 (SA) |1 Azure サブスクリプションに 1 人 |このロールには、 [Azure ポータル](https://portal.azure.com)でのサービスの管理が許可されます。 既定で、新しいサブスクリプションのアカウント管理者はサービス管理者でもあります。 RBAC では、所有者ロールは、サブスクリプション スコープでサービス管理者に付与されます。|
| 共同管理者 (CA) |サブスクリプションあたり 200 人 |サービス管理者と同じアクセス権を持っているものの、サブスクリプションと Azure ディレクトリとの関連付けを変更することはできないロール。 RBAC では、所有者ロールは、サブスクリプション スコープで共同管理者に付与されます。|

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>リソースのアクセス制御と Active Directory の詳細

* Microsoft Azure でリソース アクセスを制御する方法の詳細については、「[Azure でのリソース アクセスについて](../role-based-access-control/rbac-and-directory-admin-roles.md)」を参照してください。
* Azure Active Directory について詳しくは、「[Azure サブスクリプションを Azure Active Directory に関連付ける方法](../active-directory/active-directory-how-subscriptions-associated-directory.md)」および「[Azure Active Directory での管理者ロールの割り当て](../active-directory/active-directory-assign-admin-roles-azure-portal.md)」をご覧ください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

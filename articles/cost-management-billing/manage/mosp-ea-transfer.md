---
title: Azure サブスクリプションを Enterprise Agreement に移転する
description: この記事は、Microsoft 顧客契約または MOSP サブスクリプションを Enterprise Agreement に移転する手順を理解するのに役立ちます。
author: bandersmsft
ms.reviewer: jatracey
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/12/2021
ms.author: banders
ms.custom: ''
ms.openlocfilehash: 4350a36c63e8ea6be4284baeaba3a60d1de9099b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007153"
---
# <a name="transfer-an-azure-subscription-to-an-enterprise-agreement-ea"></a>Azure サブスクリプションを Enterprise Agreement (EA) に移転する

この記事は、Microsoft 顧客契約または MOSP (従量課金制) サブスクリプションを EA に移転するために必要な手順を理解するのに役立ちます。 移転にダウンタイムはありませんが、移転を有効にするために従うべき多くの手順が存在します。

> [!NOTE]
> 移転プロセスでは、そのサブスクリプションがリンクされている Azure AD ディレクトリの情報は変更されません。 Azure AD ディレクトリの変更を行う場合は、「[Azure サブスクリプションを別の Azure AD ディレクトリに移転する](../../role-based-access-control/transfer-subscription.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

以降のセクションは、サブスクリプション アカウント管理者、必要な宛先 EA へのアクセスを識別し、EA 認証レベルを設定するのに役立ちます。

### <a name="identify-the-subscription-account-admin"></a>サブスクリプション アカウント管理者を識別する

サブスクリプションの少なくとも Azure RBAC 閲覧者ロールを持つアカウントで Azure portal にサインインすることによって、サブスクリプション アカウント管理者を識別します。 次に、**[サブスクリプション]** に移動し、サブスクリプションを選択してから **[プロパティ]** を選択します。 そのサブスクリプションの **[アカウント管理者]** が表示されます。 ユーザー情報を書き留めておきます。

:::image type="content" source="./media/mosp-ea-transfer/subscription-account-admin.png" alt-text="[アカウント管理者] を表示できるサブスクリプションのプロパティを示す画像" lightbox="./media/mosp-ea-transfer/subscription-account-admin.png" :::

> [!NOTE]
> 前の情報で説明されているように、後で、同じアカウント管理者のユーザー アカウントで Enterprise Agreement (EA) ポータルにサインインする必要があります。 そのアカウントにアクセスできない場合は、先に進む前に、そのアクセスを取得する必要があります。 詳細については、「[Azure サブスクリプションの課金所有権を別のアカウントに譲渡する](billing-subscription-transfer.md)」を参照してください。

### <a name="access-to-the-destination-ea"></a>宛先 EA へのアクセス

EA アカウント所有者を作成するには、宛先 EA にアクセスできる必要があります。 既存の部門または新しい部門が必要です。 この部門は、サブスクリプションが移転されるときに、そのサブスクリプションが配置される場所です。 EA アカウント所有者は、その部門に配置されたサブスクリプションを所有します。

EA アカウント所有者を作成するには、次のロールのいずれかが必要です。 EA ロールの詳細については、[Azure Enterprise Agreement ロールの管理](understand-ea-roles.md)を参照してください。

- エンタープライズ管理者
- 部門管理者

### <a name="set-ea-authentication-level"></a>EA 認証レベルを設定する

EA には、加入契約の EA アカウント所有者としてどの種類のユーザーを追加できるかを決定する認証レベルが設定されています。 「[認証レベルの種類](ea-portal-troubleshoot.md#authentication-level-types)」で説明されているように、使用可能な 4 つの認証レベルがあります。

EA に設定された認証レベルを使用すると、前に書き留めたサブスクリプション アカウント管理者を使用して新しい EA アカウント所有者を作成できることを確認してください。 次に例を示します。

- サブスクリプション アカウント管理者が `@outlook.com` の電子メール アドレス ドメインを持っている場合は、EA の認証レベルが **Microsoft アカウントのみ** または **混在アカウント** のどちらかに設定されている必要があります。
- サブスクリプション アカウント管理者が `@<YourAzureADTenantPrimaryDomain.com>` の電子メール アドレス ドメインを持っている場合は、EA の認証レベルが **職場または学校アカウント** または **テナント間の職場または学校アカウント** のどちらかに設定されている必要があります。 新しい EA アカウント所有者を作成する機能は、EA の既定のドメインがサブスクリプション アカウント管理者の電子メール アドレス ドメインと同じかどうかによって異なります。

> [!NOTE]
> 正しく設定されていれば、認証レベルを変更しても移転プロセスには影響を与えません。 詳細については、「[認証レベルの種類](ea-portal-troubleshoot.md#authentication-level-types)」を参照してください。

## <a name="transfer-the-subscription-to-the-ea"></a>サブスクリプションを EA に移転する

必要な[前提条件](#prerequisites)をすべて確認し、それらを完了したら、サブスクリプションを EA に移転するプロセスを開始する準備が整いました。

### <a name="create-an-ea-account-owner"></a>EA アカウント所有者を作成する

[[前提条件]](#access-to-the-destination-ea) のセクションで説明されているように、既存の EA 部門または新しい EA 部門の一部として EA アカウント所有者を作成する必要があります。

1. エンタープライズ管理者または部門管理者として EA ポータル (https://ea.azure.com) にサインインします。
1. 新しい部門を作成するには、「[Azure エンタープライズ部署を作成する](ea-portal-administration.md#create-an-azure-enterprise-department)」を参照してください。
1. EA アカウント所有者を作成するには、[アカウントの追加](ea-portal-administration.md#add-an-account)に関するページを参照してください。 **[アカウント所有者の電子メール]** を入力するよう求められたら、前に識別したサブスクリプションの **[アカウント管理者]** の電子メール アドレスを入力します。
    > [!NOTE]
    > EA 認証レベルが間違って設定されている場合は、EA アカウント所有者の作成は失敗し、EA ポータルにエラーが表示されます。

EA アカウント所有者が作成されると、現在は EA アカウント所有者でもあるサブスクリプション アカウント管理者は Azure EA Portal から電子メールを受信します。 これは、そのユーザーに EA アカウント所有者になったことを通知します。 そのユーザーが、指定されたアカウントに関連付けられている電子メール メールボックスにアクセスできない場合でも、心配はいりません。 この電子メールは単なる通知です。 処理を続行するために、その電子メール内の情報は必要ありません。 ただし、そのサブスクリプションに関する今後の通知のためには、電子メール メールボックスが推奨されます。

### <a name="complete-the-subscription-transfer"></a>サブスクリプションの移転を完了する

これで、サブスクリプション アカウント管理者が EA アカウント所有者にもなったので、この管理者は EA の下にサブスクリプションを作成できます。

> [!IMPORTANT]
> 先に進む前に、新しい EA アカウント所有者が初めて EA ポータルにサインインするとどうなるかを理解する必要があります。 EA ポータルでサブスクリプション アカウント管理者としてサインインする前に、次の情報を読んで理解してください。

新しい EA アカウント所有者が初めて EA ポータルにサインインすると、次の警告が表示されます。

```
WARNING

You are about to associate your account (email address) to the following enrollment:

Enrollment Name: <EnrollmentName>
Enrollment Number: <EnrollmentNumber>

All Enrollment Administrators can gain access to all of your subscriptions if you proceed.
Additionally, all Azure subscriptions for which you are the account owner will be converted to your Enterprise Agreement.
This includes subscriptions which include a monthly credit (e.g. Visual Studio, MPN, BizSpart, etc.) meaning you will lose the monthly credit by proceeding.
All subscriptions based on a Visual Studio subscriber offer (monthly credit for Visual Studio subscribers or Pay-As-You-Go Dev/Test) will be converted to use the Enterprise Dev/Test usage rates and be billed against this enrollment from today onwards.
If you wish to retain the monthly credits currently associated with any of your subscriptions, please cancel.
Please see additional details.

Cancel  Continue
```

「[Azure EA Portal の管理](ea-portal-administration.md#enterprise-devtest-offer)」に記載されているように、この警告は次のことを示しています。

***ユーザーが Azure EA Portal 経由でアカウント所有者として追加されると、そのアカウント所有者に関連付けられている、MOSP (PAYG) Dev/Test プランまたは Visual Studio サブスクライバー向けの月単位のクレジット プランのどちらかに基づいた Azure サブスクリプションはすべて EA Dev/Test プランに変換されます。そのアカウント所有者に関連付けられている、その他のプランの種類 (MOSP (PAYG) など) に基づいたサブスクリプションは、標準の EA サブスクリプション プランに変換されます。***

ユーザーがこの警告の結果を理解した場合は、**[続行]** を選択すると、そのアカウントに関連付けられているサブスクリプションが EA に移転されます。

## <a name="more-help"></a>その他のヘルプ

前の情報を使用して、サブスクリプションを EA に移転している必要があります。 さらにサポートが必要な場合は、[課金サポート リクエスト](https://go.microsoft.com/fwlink/?linkid=2083458)を作成してください。

## <a name="next-step"></a>次のステップ

- [Azure エンタープライズ ポータルを使い始める](ea-portal-get-started.md)

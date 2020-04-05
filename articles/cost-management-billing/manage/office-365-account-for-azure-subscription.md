---
title: Office 365 アカウントを使用して Azure にサインアップする
description: Office 365 アカウントを使用して、Azure サブスクリプションを作成する方法を説明します
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: b42a5b83f5442755614a3cb2ae81cffda09b4b3b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238110"
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Office 365 アカウントを使用して Azure のサブスクリプションにサインアップする
Office 365 サブスクリプションがある場合は、Office 365 アカウントを使用して Azure サブスクリプションを作成できます。 Office 365 のユーザー名とパスワードを使用して [Azure ポータル](https://portal.azure.com/) にサインインします。 仮想マシンを設定するか、他の Azure サービスを使用する場合は、Azure サブスクリプションにサインアップする必要があります。 他のユーザーと Azure サブスクリプションを共有し、[ロールベースのアクセス制御を使用して、Azure サブスクリプションとリソースへのアクセスを管理できます](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

既に Office 365 アカウントと Azure サブスクリプションの両方がある場合は、「[Office 365 テナントの Azure サブスクリプションへの関連付け](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)」を参照してください。

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Office 365 アカウントを使用して Azure サブスクリプションを取得する

Office 365 のユーザー名とパスワードを使用して Azure にサインアップすることで、時間を節約し、アカウントの増加を回避します。

1. [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs) にサインアップします。
2. Office 365 のユーザー名とパスワードを使用してサインインします。 使用するアカウントでは、管理者のアクセス許可は必要ありません。 複数の Office 365 アカウントを持っている場合、Azure サブスクリプションに関連付ける Office 365 アカウントの資格情報を使用していることを確認します。

   ![サインイン ページを示すスクリーン ショット。](./media/office-365-account-for-azure-subscription/billing-sign-in-with-office-365-account.png)

3. 必要な情報を入力し、サインアップ プロセスを完了します。 Office 365 アカウントが既にある場合、一部の情報は不要である可能性があります。

    ![サインアップ フォームを示すスクリーン ショット。](./media/office-365-account-for-azure-subscription/billing-azure-sign-up-fill-information.png)

- Azure サブスクリプションに組織内の他のユーザーを追加する必要がある場合は、[Azure ポータルでのアクセス管理の開始](../../role-based-access-control/overview.md)に関する記事を参照してください。

## <a name=""></a><a id="more-about-subs">Azure サブスクリプションと Office 365 サブスクリプションの背景情報</a>
Office 365 と Azure では、ユーザーとサブスクリプションの管理に Azure AD サービスが使用されています。 Azure のディレクトリは、ユーザーとサブスクリプションをグループ化するコンテナーのようなものです。 Azure サブスクリプションと Office 365 サブスクリプションで同じユーザー アカウントを使用するには、Azure サブスクリプションを Office 365 サブスクリプションと同じディレクトリに作成する必要があります。 以下の点に注意してください。

* サブスクリプションはディレクトリに作成されます。
* ユーザーはディレクトリに属しています。
* サブスクリプションは、サブスクリプションを作成したユーザーのディレクトリに属します。 そのため、Office 365 サブスクリプションは、Azure サブスクリプションと同じアカウントに関連付けられます。
* Azure サブスクリプションの所有者は、ディレクトリ内の各ユーザーです。
* Office 365 サブスクリプションの所有者は、ディレクトリ自体です。 ディレクトリ内で適切なアクセス許可を持つユーザーは、これらのサブスクリプションを管理できます。

![ディレクトリとユーザー、サブスクリプションの関係を示すスクリーン ショット。](./media/office-365-account-for-azure-subscription/19-background-information.png)

詳細については、「 [Azure サブスクリプションを Azure Active Directory に関連付ける方法](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)」を参照してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

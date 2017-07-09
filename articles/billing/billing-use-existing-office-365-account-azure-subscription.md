---
title: "Office 365 アカウントを使用して Azure にサインアップする | Microsoft Docs"
description: "Office 365 アカウントを使用して、Azure サブスクリプションを作成する方法を説明します"
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: cjiang
ms.translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: d62ec023605573e4b0804da00c75f65c8dea5173
ms.contentlocale: ja-jp
ms.lasthandoff: 04/12/2017


---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Office 365 アカウントを使用して Azure のサブスクリプションにサインアップする
Office 365 サブスクリプションがある場合は、Office 365 アカウントを使用して、Azure サブスクリプションを作成します。 Office 365 のユーザー名とパスワードを使用すると、Azure サブスクリプションがなくても、[Azure ポータル](https://portal.azure.com/)にサインインできます。 ただし、仮想マシンを設定したり、他の Azure サービスを使用する場合は、アカウント用の Azure サブスクリプションを作成する必要があります。 Azure サービスを使用する必要がある組織に所属している場合、サブスクリプションを作成したあとで、組織内の他のユーザーをサブスクリプションに追加します。  

既に Office 365 アカウントと Azure サブスクリプションの両方がある場合は、「[Office 365 テナントの Azure サブスクリプションへの関連付け](billing-add-office-365-tenant-to-azure-subscription.md)」を参照してください。

## <a name="get-an-azure-subscription-by-using-your-office-365-account"></a>Office 365 アカウントを使用して Azure サブスクリプションにサインアップする

Azure にサインアップするには、Azure 無料リンクを使用し、Office 365 のユーザー名とパスワードを使用して、Azure にサインアップします。 

1. [Azure.com](https://azure.microsoft.com/) に移動します。 
2. **[無料で始める]** をクリックします。
3. Office 365 のユーザー名とパスワードを使用してサインインします。 使用するアカウントでは、管理者のアクセス許可は必要ありません。 複数の Office 365 アカウントを持っている場合、Azure サブスクリプションに関連付ける Office 365 アカウントの資格情報を使用していることを確認します。 

   ![サインイン ページを示すスクリーン ショット。](./media/billing-use-existing-office-365-account-azure-subscription/billing-sign-in-with-office-365-account.png)

4. 必要な情報を入力し、サインアップ プロセスを完了します。

    ![サインアップ フォームを示すスクリーン ショット。](./media/billing-use-existing-office-365-account-azure-subscription/billing-azure-sign-up-fill-information.png)

- Azure サブスクリプションに組織内の他のユーザーを追加する必要がある場合は、「[Get started with access management in the Azure portal (Azure Portal におけるアクセス管理の概要)](../active-directory/role-based-access-control-what-is.md)」を参照してください。 
- サブスクリプションを管理する管理者を追加する場合は、「[サブスクリプションまたはサービスを管理する Azure 管理者ロールを追加または変更する](billing-add-change-azure-subscription-administrator.md)」を参照してください。

## <a id="more-about-subs">Azure サブスクリプションと Office 365 サブスクリプションの背景情報</a>
Office 365 と Azure では、ユーザーとサブスクリプションの管理に Azure AD サービスが使用されています。 Azure のディレクトリは、ユーザーとサブスクリプションをグループ化するコンテナーのようなものです。 Azure サブスクリプションと Office 365 サブスクリプションに同じユーザー アカウントを使用するには、同じディレクトリにサブスクリプションを作成する必要があります。 以下の点に注意してください。

* サブスクリプションは、ディレクトリに作成されます。
* ユーザーは、ディレクトリに属しています。
* サブスクリプションは、サブスクリプションを作成したユーザーのディレクトリに属します。 そのため、Office 365 サブスクリプションは、Azure サブスクリプションと同じアカウントに関連付けられます。
* Azure サブスクリプションの所有者は、ディレクトリ内の各ユーザーです。
* Office 365 サブスクリプションの所有者は、ディレクトリ自体です。 ディレクトリ内で適切なアクセス許可を持つユーザーは、これらのサブスクリプションを管理できます。

![ディレクトリとユーザー、サブスクリプションの関係を示すスクリーン ショット。](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

詳細については、「 [Azure サブスクリプションを Azure Active Directory に関連付ける方法](../active-directory/active-directory-how-subscriptions-associated-directory.md)」を参照してください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。 

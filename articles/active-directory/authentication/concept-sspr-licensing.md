---
title: セルフサービスによるパスワード リセットのライセンス - Azure Active Directory
description: Azure AD のセルフ サービスによるパスワード リセットのライセンス要件
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 78d4d721f2821a8365185c0bad6d795c67a75292
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2018
ms.locfileid: "33864666"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Azure AD のセルフ サービスによるパスワード リセットのライセンス要件

Azure Active Directory (Azure AD) のパスワード リセットを機能させるには、*ご自分の組織に少なくとも 1 つライセンスが割り当てられている必要があります*。 パスワード リセット エクスペリエンスに関して、ユーザーごとのライセンスは強制していません。 ユーザーがこのライセンスで提供される機能を直接的または間接的を問わず利用するには、適切なライセンスが必要になります。

* **クラウドのみのユーザー**: Office 365 のすべての有料 SKU、または Azure AD Basic
* **クラウド**または**オンプレミス ユーザー**: Azure AD Premium P1 または P2、Enterprise Mobility + Security (EMS)、または Microsoft 365

## <a name="licenses-required-for-password-writeback"></a>パスワード ライトバックで必要なライセンス

パスワード ライトバックを使用するには、次のいずれかのライセンスがご自分のテナントに割り当てられている必要があります。

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 E3
* Microsoft 365 E5
* Microsoft 365 F1

> [!WARNING]
> スタンドアロンの Office 365 ライセンス プランは、*パスワード ライトバックをサポートしていません*。この機能を動作させるには、上記プランのいずれかが必要になります。
>

追加のライセンス情報 (コストを含む) については、以下のページをご覧ください。

* [Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory の機能と働き](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>グループまたはユーザー ベースのライセンスの有効化

現在、AzureAD はグループベースのライセンスをサポートしています。 管理者はライセンスを一度に 1 つずつユーザーに割り当てるのではなく、ユーザーのグループに一括して割り当てることができます。 詳細については、[ライセンスの割り当て、検証、および問題の解決](../active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses)に関するページを参照してください。

Microsoft サービスの中には、すべての場所では利用できないものもあります。 ライセンスをユーザーに割り当てる前に、管理者はユーザーの **[利用場所]** プロパティを指定しておく必要があります。 ライセンスの割り当ては、Azure Portal の **[ユーザー]** > **[プロファイル]** > **[設定]** セクションで行います。 *グループでのライセンス割り当てを使用する場合、利用場所が指定されていないユーザーは、ディレクトリの場所を継承します。*

## <a name="next-steps"></a>次の手順

* [SSPR のロールアウトを正常に完了する方法](howto-sspr-deployment.md)
* [パスワードのリセットまたは変更](../active-directory-passwords-update-your-own-password.md)
* [セルフサービスのパスワード リセットのための登録](../active-directory-passwords-reset-register.md)
* [SSPR が使用するデータと、ユーザー用に事前設定が必要なデータ](howto-sspr-authenticationdata.md)
* [ユーザーが使用できる認証方法](concept-sspr-howitworks.md#authentication-methods)
* [SSPR のポリシー オプション](concept-sspr-policy.md)
* [パスワード ライトバックの概要とその必要性](howto-sspr-writeback.md)
* [SSPR でアクティビティをレポートする方法](howto-sspr-reporting.md)
* [SSPR のすべてのオプションとその意味](concept-sspr-howitworks.md)
* [不具合が発生していると思われるSSPR のトラブルシューティング方法](active-directory-passwords-troubleshoot.md)
* [質問したい内容に関する説明がどこにもない。](active-directory-passwords-faq.md)

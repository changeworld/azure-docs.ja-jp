---
title: "ライセンス: Azure AD SSPR | Microsoft Docs"
description: "Azure AD のセルフ サービスによるパスワード リセットのライセンス要件"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: db849cd1e9da634064f79fbc041098542580ad02
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2017
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Azure AD のセルフ サービスによるパスワード リセットのライセンス要件

Azure AD のパスワード リセットが機能するには、**組織で少なくとも 1 つのライセンスが割り当てられている必要があります**。 パスワード リセット エクスペリエンスでユーザーごとのライセンスを適用することはありません。 Microsoft ライセンス契約とのコンプライアンスを維持するには、プレミアム機能を使用するすべてのユーザーにライセンスを割り当てる必要があります。

* **クラウド ユーザーのみ** - Office 365 (O365) のすべての有料 SKU、または Azure AD Basic
* **クラウド**および/または**内部設置型ユーザー** - Azure AD Premium P1 または P2、Enterprise Mobility + Security (EMS)、または Secure Productive Enterprise (SPE)

## <a name="licenses-required-for-password-writeback"></a>パスワード ライトバックで必要なライセンス

パスワード ライトバックを使用するのには、次のライセンスのいずれかがテナントに割り当てられている必要があります。

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 E3
* Microsoft 365 E5

> [!WARNING]
> スタンドアロンの Office 365 ライセンス プランは、**パスワード ライトバックをサポートしていません**。この機能を動作させるには、上記プランのいずれかが必要になります。

追加のライセンス情報 (コストを含む) については、以下のページをご覧ください。

* [Azure Active Directory の料金サイト](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft Azure Active Directory の機能と働き](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>グループまたはユーザー ベースのライセンスの有効化

現在、Azure AD はグループ ベースのライセンスをサポートしています。これにより、管理者はライセンスを一度に 1 つずつユーザーに割り当てるのではなく、ライセンスをユーザーのグループに一括して割り当てることができます。 [ライセンスの割り当て、検証、および問題の解決](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses)

一部の Microsoft サービスは、すべての場所で利用できないことがあります。 ライセンスをユーザーに割り当てる前に、管理者はユーザーの [利用場所] プロパティを指定しておく必要があります。 ライセンスの割り当ては、Azure Portal の [ユーザー] > [プロファイル] > [設定] セクションで行います。 **グループ ライセンス割り当てを使用するとき、利用場所が指定されていないユーザーは、ディレクトリの場所を継承します。**

## <a name="next-steps"></a>次のステップ

* [SSPR のロールアウトを適切に完了する方法。](active-directory-passwords-best-practices.md)
* [パスワードのリセットと変更。](active-directory-passwords-update-your-own-password.md)
* [セルフサービスによるパスワード リセットの登録。](active-directory-passwords-reset-register.md)
* [SSPR が使用するデータと、ユーザー用に設定するデータ。](active-directory-passwords-data.md)
* [ユーザーが使用できる認証方法。](active-directory-passwords-how-it-works.md#authentication-methods)
* [SSPR のポリシー オプション。](active-directory-passwords-policy.md)
* [パスワード ライトバックと、それが必要な理由。](active-directory-passwords-writeback.md)
* [SSPR でアクティビティをレポートする方法。](active-directory-passwords-reporting.md)
* [SSPR のすべてのオプションとその意味。](active-directory-passwords-how-it-works.md)
* [エラーが発生していると思われる場合のSSPR のトラブルシューティング方法。](active-directory-passwords-troubleshoot.md)
* [質問したい内容に関する説明がどこにもない。](active-directory-passwords-faq.md)

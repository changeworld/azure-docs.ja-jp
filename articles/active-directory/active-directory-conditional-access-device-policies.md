---
title: "Office 365 サービス用の Azure Active Directory の条件付きアクセスのデバイス ポリシー | Microsoft Docs"
description: "ユーザー コンプライアンスやサービスへのアクセスを維持しながら、企業のリソースをさらに一層セキュリティで保護された状態にできる、条件付きアクセスのデバイス ポリシーをプロビジョニングする方法について説明します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8664c0bb-bba1-4012-b321-e9c8363080a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 2b586aee9559b62d4cc8b21ab88ab193e61e7c14
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="active-directory-conditional-access-device-policies-for-office-365-services"></a>Office 365 サービス用の Active Directory の条件付きアクセスのデバイス ポリシー

条件付きアクセスでは、複数の要素が正常に機能することが求められます。 これには、特に多要素認証されたユーザー、認証済みデバイス、および準拠しているデバイスが関係してきます。 この記事では、組織における Office 365 サービスへのアクセスの制御に役立つ、デバイス ベースの条件に主に焦点を合わせています。 

企業ユーザーは、個人のデバイスから勤務先または学校の Exchange や SharePoint Online などの Office 365 サービスにアクセスすることを望んでいます。 そのようなアクセスは、セキュリティで保護されている必要があります。 準拠しているデバイスを使用しているユーザーにサービスへのアクセス権を付与しながら、企業のリソースをさらに一層セキュリティで保護された状態にできる、条件付きアクセスのデバイス ポリシーのプロビジョニングが可能です。 Office 365 に対する条件付きアクセス ポリシーは、Microsoft Intune の条件付きアクセス ポータルで設定できます。

Azure Active Directory (Azure AD) は 、Office 365 サービスへのアクセス確保に役立つ条件付きアクセス ポリシーを適用します。 非準拠のデバイスを使用しているユーザーが Office 365 サービスにアクセスできないようにブロックする条件付きアクセス ポリシーを作成できます。 ユーザーは、サービスへのアクセス権が付与される前に、社内のデバイス ポリシーに準拠している必要があります。 もう一つの方法として、Office 365 サービスにアクセスするにはユーザーによるデバイス登録が必要になる、というポリシーの作成が可能です。 ポリシーは、組織内のすべてのユーザーに適用することも、いくつかのターゲット グループに制限することもできます。 より多くのターゲット グループを徐々にポリシーに追加できます。

デバイス ポリシーを適用するための前提条件として、ユーザーは自分のデバイスを Azure AD デバイス登録サービスに登録する必要があります。 Azure AD デバイス登録サービスに登録するデバイスの多要素認証を有効にする、という選択も可能です。 Azure Active Directory デバイス登録サービスには多要素認証をお勧めします。 多要素認証が有効になっている場合、Azure AD デバイス登録サービスに使用するデバイスを登録しているユーザーは、第 2 要素による認証へのチャレンジが求められます。

## <a name="how-does-a-conditional-access-policy-work"></a>条件付きアクセス ポリシーのしくみ

サポートされているデバイス プラットフォームから、ユーザーが Office 365 サービスへのアクセスを要求すると、Azure AD によってそのユーザーとデバイスが認証されます。 Azure AD は、ユーザーがサービスに対して設定されているポリシーに準拠している場合にのみ、そのサービスへのアクセス権を付与します。 登録されていないデバイスを使用しているユーザーには、登録方法や社内の Office 365 サービスにアクセスするために準拠する方法に関する手順が示されます。 iOS および Android デバイスを使用しているユーザーは、Intune ポータル サイト アプリケーションを使用して自分のデバイスを登録する必要があります。 ユーザーがデバイスを登録すると、そのデバイスは Azure AD に登録されます。これでデバイス管理とコンプライアンスについて登録済みの状態となります。 Office 365 サービスに対するモバイル デバイスの管理を行うには、Azure AD デバイス登録サービスと Microsoft Intune を併用する必要があります。 デバイス ポリシーが適用されている場合、ユーザーが Office 365 サービスにアクセスするにはデバイスの登録が必要です。

ユーザーによるデバイス登録が成功すると、そのデバイスは信頼済みの状態となります。 Azure AD は、認証されたユーザーに社内のアプリケーションへのシングル サインオン アクセスを提供します。 Azure AD は、ユーザーが初めてアクセスを要求したときだけでなく、アクセスへの要求を更新するたびに、サービスへのアクセス権を付与するための条件付きアクセス ポリシーを適用します。 サインインの資格情報が変更された場合、対象デバイスが紛失するか盗まれた場合、または更新が要求された時点でポリシーの条件が満たされていない場合に、ユーザーはサービスへのアクセスを拒否されます。

## <a name="deployment-considerations"></a>デプロイに関する考慮事項

デバイスを登録するには、Azure AD デバイス登録サービスを使用する必要があります。

オンプレミスのユーザーの認証が予定されている場合は、Active Directory フェデレーション サービス (AD FS) (バージョン 1.0 以降のバージョン) が必要です。 ID プロバイダーが多要素認証に対応していない場合、Workplace Join への多要素認証は失敗します。 たとえば、AD FS 2.0 では多要素認証を使用できません。 オンプレミスの AD FS が多要素認証に対応していること、および Azure AD デバイス登録サービスへの多要素認証を有効にする前に、有効な多要素認証方法の準備が整っていることを確認してください。 たとえば、Windows Server 2012 R2 上の AD FS には多要素認証機能があります。 また、Azure AD デバイス登録サービスへの多要素認証を有効にする前に、AD FS サーバー上で有効な認証 (多要素認証) 方法を追加設定することも必要です。 AD FS でサポートされている多要素認証方法の詳細については、「[Configure additional authentication methods for AD FS (AD FS の追加の認証方法の構成 )](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)」を参照してください。

## <a name="next-steps"></a>次のステップ

*   一般的な質問への答えについては、「[Azure Active Directory conditional access FAQs (Azure Active Directory の条件付きアクセスのよく寄せられる質問)](active-directory-conditional-faqs.md)」を参照してください。

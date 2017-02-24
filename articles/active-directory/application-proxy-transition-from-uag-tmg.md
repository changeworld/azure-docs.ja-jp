---
title: "Microsoft Forefront から Azure AD アプリケーション プロキシへの切り替え | Microsoft Docs"
description: "Azure AD アプリケーション プロキシ コネクタの基本について説明します。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 9945512d18d66c321c0d24ee1050497d4716fd47
ms.openlocfilehash: 699112846cc1e4e9fc6b04b1b8509152d7aecdef


---
#<a name="transition-to-azure-ad-app-proxy-from-microsoft-forefront"></a>Microsoft Forefront から Azure AD アプリケーション プロキシへの切り替え

Microsoft Forefront の Threat Management Gateway (TMG) および United Access Gateway (UAG) ソリューションから、これらの Azure AD アプリケーション プロキシである Web アプリケーション プロキシ (WAP) および Azure AD アプリケーション プロキシ (AADAP) に移行する方法について説明します。 

> [!NOTE]
> アプリケーション プロキシは、Azure Active Directory の Premium または Basic エディションにアップグレードしている場合にのみ利用できる機能です。 詳細については、「[Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。
> 
 
多くのお客様から、Forefront UAG と TMG から新しいアプリケーション プロキシに移行する方法について質問を受けています。 詳しくは、切り替えについて詳細に説明しているこの[ホワイト ペーパー](http://download.microsoft.com/download/3/E/3/3E335D93-6DB8-4834-90A8-B86105419F05/Microsoft%20TMG%20and%20UAG%20EOL%20and%20transitioning%20to%20WAP%20and%20AADAP.docx)を参照してください。 
 
## <a name="tmguag-conversion-to-azure-ad-application-proxy-table"></a>TMG/UAG から Azure AD アプリケーション プロキシへの変換表
 
|**TMG/UAG の機能**|**Web アプリケーション プロキシ (WAP) / Azure AD アプリケーション プロキシ (AADAP)**|
|:-----|:-----|
|ブラウザー アプリケーションの選択的な HTTP の公開|Windows Server 2012 R2 の WAP で利用可能 (現在は AADAP で利用可能)|
|ADFS 統合|Windows Server 2012 R2 の WAP で利用可能 (現在は AADAP で利用可能)|
|さまざまなプロトコルでの公開 (Citrix、Lync、RDG など)|Windows Server 2012 R2 の WAP で利用可能 (現在は AADAP で利用可能)|
|ActiveSync (HTTP Basic) および RDG の事前認証|Windows Server vNext の WAP で対応予定 (AADAP で対応予定)|
|ポータル|WAP向け Intune / System Center の使用 (AAD アクセス パネルまたは AADAP で利用可能な Office 365 アプリ起動ツールの使用)|
|エンドポイントの正常性の検出|Intune / System Center の使用|
|SSL トンネリング|Windows SSL /VPN 機能の使用|
|レイヤー 2/3 ファイアウォール|Windows Server 機能の使用|
|Web アプリケーション ファイアウォール|現在は Microsoft からのソリューションはありません|
|安全な Web Gateway (転送プロキシ)|現在は Microsoft からのソリューションはありません|


## <a name="next-steps"></a>次のステップ

[ブログ: Web アプリケーション プロキシ](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[ブログ: アプリケーション プロキシ](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap)



<!--HONumber=Feb17_HO1-->



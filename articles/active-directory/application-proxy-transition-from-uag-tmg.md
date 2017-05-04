---
title: "Microsoft Forefront から Azure AD アプリケーション プロキシへの切り替え | Microsoft Docs"
description: "Microsoft Forefront の TMG および UAG ソリューションから Azure Active Directory アプリケーション プロキシに移行する方法に関する基本事項について説明します。"
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
ms.date: 04/17/2017
ms.author: kgremban
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 6e95e9abac988ae54a401927a92bdb397dd63eed
ms.lasthandoff: 04/21/2017

---
# <a name="transition-to-azure-ad-application-proxies-from-microsoft-forefront"></a>Microsoft Forefront から Azure AD アプリケーション プロキシへの切り替え

この記事では、Microsoft Forefront の Threat Management Gateway (TMG) および Unified Access Gateway (UAG) ソリューションから Azure AD アプリケーション プロキシに移行する方法について説明します。

Forefront の TMG および UAG からアプリケーション プロキシへの切り替えについて詳しくは、[Microsoft の関連するホワイト ペーパーをご覧ください](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/)。

## <a name="functionality-details-for-the-conversion"></a>変換に関する機能の詳細

|**TMG/UAG の機能**|**Web アプリケーション プロキシ/Azure AD アプリケーション プロキシ**|
|:-----|:-----|
|ブラウザー アプリケーションの選択的な HTTP の公開|Windows Server 2012 R2 の Web アプリケーション プロキシで利用可能。 現在は Azure AD アプリケーション プロキシで利用可能。|
|Active Directory フェデレーション サービス (AD FS) 統合|Windows Server 2012 R2 の Web アプリケーション プロキシで利用可能。 現在は Azure AD アプリケーション プロキシで利用可能。|
|さまざまなプロトコルでの公開 (Citrix、Lync、RDG など)|Windows Server 2012 R2 の Web アプリケーション プロキシで利用可能。 現在は Azure AD アプリケーション プロキシで利用可能。|
|ActiveSync (HTTP Basic) および RDG の事前認証|Web アプリケーション プロキシおよび Azure AD アプリケーション プロキシでは現在利用できません。|
|ポータル|Web アプリケーション プロキシで Intune または System Center を使用。 Azure AD アプリケーション プロキシで Azure AD アクセス パネルまたは Office 365 アプリ起動ツールを使用。|
|エンドポイントの正常性の検出|Intune または System Center を使用。|
|SSL トンネリング|Windows SSL または VPN 機能を使用。|
|レイヤー 2/3 ファイアウォール|Windows Server の機能を使用。|
|Web アプリケーション ファイアウォール|現在、Microsoft のソリューションはありません。|
|セキュリティ保護された Web ゲートウェイ (転送プロキシ)|現在、Microsoft のソリューションはありません。|


## <a name="next-steps"></a>次のステップ

[ブログ: Web アプリケーション プロキシ](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[ブログ: Azure AD アプリケーション プロキシ](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap)


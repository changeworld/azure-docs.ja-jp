---
title: "Azure AD アプリケーション プロキシへのアップグレード | Microsoft Docs"
description: "Microsoft Forefront または Unified Access Gateway からアップグレードする場合に最適なプロキシ ソリューションを選択します。"
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
ms.date: 04/27/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: e9d5caa4d11012744ce9f26648166371f3aa17ba
ms.contentlocale: ja-jp
ms.lasthandoff: 05/13/2017

---
# <a name="upgrade-to-azure-ad-proxies-from-microsoft-forefront-or-unified-access-gateway"></a>Microsoft Forefront または Unified Access Gateway から Azure AD プロキシへのアップグレード

この記事では、Microsoft Forefront の Threat Management Gateway (TMG) および Unified Access Gateway (UAG) ソリューションから Azure AD アプリケーション プロキシに移行する方法について説明します。

Forefront の TMG および UAG からアプリケーション プロキシへの切り替えについて詳しくは、[Microsoft の関連するホワイト ペーパーをご覧ください](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/)。

## <a name="functionality-details-for-the-conversion"></a>変換に関する機能の詳細

|**TMG/UAG の機能**|**最新のソリューション**|
|:-----|:-----|
|ブラウザー アプリケーションの選択的な HTTP の公開|Azure AD アプリケーション プロキシ|
|Active Directory フェデレーション サービス (AD FS) 統合|Azure AD アプリケーション プロキシ|
|さまざまなプロトコルでの公開 (Citrix、Lync、RDG など)|Azure AD アプリケーション プロキシ|
|ポータル|Azure AD アプリケーション プロキシの Azure AD アクセス パネルまたは Office 365 アプリ起動ツール|
|エンドポイントの正常性の検出|Intune または System Center|
|SSL トンネリング|Windows SSL または VPN|
|レイヤー 2/3 ファイアウォール|Windows Server|
|ActiveSync (HTTP Basic) および RDG の事前認証|現在は Microsoft からのソリューションはありません|
|Web アプリケーション ファイアウォール|現在、Microsoft のソリューションはありません。|
|セキュリティ保護された Web ゲートウェイ (転送プロキシ)|現在、Microsoft のソリューションはありません。|


## <a name="next-steps"></a>次のステップ

[ブログ: Web アプリケーション プロキシ](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[ブログ: Azure AD アプリケーション プロキシ](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap)


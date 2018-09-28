---
title: 各国のクラウドでの Azure AD を使用した認証
description: 各国のクラウドのアプリ登録と認証エンドポイントについて説明します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: negoe
ms.reviewer: negoe,andret,saeeda
ms.custom: aaddev
ms.openlocfilehash: 866a86178d66b7b4af069d684e4eb56c12db47ca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982003"
---
# <a name="national-clouds"></a>各国のクラウド

各国のクラウド (ソブリン クラウドとも呼ばれます) は、物理的に分離された Azure インスタンスです。 Azure のこれらのリージョンは、データの保存場所、主権、およびコンプライアンス要件が地理的な境界内で確実に守られるように設計されています。

グローバルなクラウドを含む Azure Active Directory は、次の各国のクラウドにデプロイされています。  

- Azure US Government
- Azure Germany
- Azure China 21Vianet

## <a name="app-registration-endpoints"></a>アプリ登録エンドポイント

次の表は、各国のクラウドにアプリケーションを登録するために使用される Azure Active Directory (Azure AD) エンドポイントのベース URL を示しています。

| 各国のクラウド | Azure AD ポータル エンドポイント
| --- | --- |
| 米国政府向け Azure AD |https://portal.azure.us
|Azure AD Germany |https://portal.microsoftazure.de
|21Vianet が運営する Azure AD China |https://portal.azure.cn
|Azure AD (グローバル サービス)|https://portal.azure.com

## <a name="azure-ad-authentication-endpoints"></a>Azure AD 認証エンドポイント

次の表は、各国のクラウドで Microsoft Graph を呼び出すトークンを取得するために使用される Azure Active Directory (Azure AD) エンドポイントのベース URL を示しています。

| 各国のクラウド | Azure AD 認証エンドポイント
| --- | --- |
| 米国政府向け Azure AD |`https://login.microsoftonline.us`
|Azure AD Germany| `https://login.microsoftonline.de`
|21Vianet が運営する Azure AD China | `https://login.chinacloudapi.cn`
|Azure AD (グローバル サービス)|`https://login.microsoftonline.com`

Azure AD の承認またはトークン エンドポイントに対する要求は、リージョン固有の適切なベース URL を使用して構成できます。 たとえば、ドイツの場合:

- 承認共通エンドポイントは `https://login.microsoftonline.de/common/oauth2/authorize` です
- トークン共通エンドポイントは `https://login.microsoftonline.de/common/oauth2/token` です 

シングルテナント アプリケーションの場合は、上記 URL の common をテナント ID または名前 (たとえば `https://login.microsoftonline.de/contoso.com`) に置き換えます

>[!NOTE]
> [Azure AD v2.0 承認]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview)およびトークン エンドポイントは、グローバル サービスにのみ使用できます。 各国のクラウドのデプロイではまだサポートされていません。

## <a name="next-steps"></a>次の手順

- [Azure Government](https://docs.microsoft.com/azure/azure-government/) の詳細
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/) の詳細
- [Azure Germany](https://docs.microsoft.com/azure/germany/) の詳細
- [Azure AD 認証の基本](authentication-scenarios.md)の詳細
- [各国のクラウドでの Microsoft Graph デプロイ](https://developer.microsoft.com/graph/docs/concepts/deployments)の詳細
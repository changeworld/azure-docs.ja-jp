---
title: アプリケーション プロキシ アプリケーションでアプリケーション ページが正しく表示されない | Microsoft Docs
description: Azure AD と統合されたアプリケーション プロキシ アプリケーションで、ページが正しく表示されない場合のガイダンス
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: asteen
ms.openlocfilehash: d15c02d86dc4a61e7e92b4c896cfc13dd0218c61
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2018
ms.locfileid: "34070534"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>アプリケーション プロキシ アプリケーションでアプリケーション ページが正しく表示されない

この記事は、ページに移動したときにページの一部が正しく表示されない場合に、Azure Active Directory アプリケーション プロキシ アプリケーションに関する問題をトラブルシューティングするのに役立ちます。

## <a name="overview"></a>概要
アプリケーション プロキシ アプリを発行する場合、アプリケーションにアクセスするときに、ルートの下にあるページのみがアクセス可能です。 ページが正しく表示されない場合、アプリケーションで使用するルートの内部 URL で、ページ リソースの一部が見つからない可能性があります。 この問題を解決するには、ページの*すべて*のリソースがアプリケーションの一部として発行されていることを確認します。

ネットワーク トラッカー (Fiddler や Internet Explorer/Edge で F12 キーを押すなどのツール) を開き、ページを読み込み、404 エラーを見つけることにより、リソースが見つからないことが問題かどうかを確認できます。 これは、現在見つけることができず、発行する必要があるページを示します。

この場合の例として、内部 URL http://myapps/expenses を使用して経費アプリケーションを発行したが、アプリではスタイルシート http://myapps/style.css を使用していると仮定します。 この場合、スタイル シートがアプリケーションでは発行されていないため、経費アプリケーションが style.css を読み込もうとするときに 404 エラーをスローします。 この例では、内部 URL http://myapp/ を使用してアプリケーションを発行することにより、問題が解決されます。

## <a name="problems-with-publishing-as-one-application"></a>1 つのアプリケーションとしての発行による問題

同じアプリケーション内のすべてのリソースを公開しない場合は、複数のアプリケーションを発行し、それらのアプリケーション間のリンクを有効にする必要があります。

その場合は、[カスタム ドメイン](manage-apps/application-proxy-configure-custom-domain.md) ソリューションを使用することをお勧めします。 ただし、このソリューションでは、ドメイン用の証明書を所有し、アプリケーションが完全修飾ドメイン名 (FQQN) を使用する必要があります。 その他のオプションについては、「[壊れたリンクのトラブルシューティングに関するドキュメント](application-proxy-page-links-broken-problem.md)」を参照してください。

## <a name="next-steps"></a>次の手順
[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](manage-apps/application-proxy-publish-azure-portal.md)

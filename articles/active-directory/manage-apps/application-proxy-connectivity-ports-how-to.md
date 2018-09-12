---
title: アプリケーション プロキシ アプリケーションに必要なファイアウォール ポートを開く方法 | Microsoft Docs
description: Azure AD アプリケーション プロキシを正しく動作させるために開くポートを確認します
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: af43b1dd0a6ccb60ba6911bce8dd540aed5c0db4
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2018
ms.locfileid: "44355381"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>アプリケーション プロキシ アプリケーションに必要なファイアウォール ポートを開く方法

必要なポートと各ポートの機能を網羅したリストについては、[アプリケーション プロキシに関するドキュメント](application-proxy-enable.md)の前提条件のセクションを参照してください。 アプリケーション プロキシでは送信ポートのみを使用する点に注意してください。

オンプレミスのネットワークから[コネクタ ポート テスト ツール](https://aadap-portcheck.connectorporttest.msappproxy.net/)を開き、必要なすべてのポートが開いているかどうかを確認することもできます。 緑色のチェックマークが多いほど、回復性が高いことになります。 

## <a name="app-proxy-regions"></a>アプリ プロキシのリージョン

お客様にとってどのリージョンに緑色のチェックマークが必要かという点については、今後お伝えできるように準備を進めています。 当面は、すべてのリージョンにチェックマークが付いていることを確認してください。 お住まいのリージョンとは無関係に、米国中部も必要なリージョンとなります。

ツールで正しい結果が得られるようにするには、以下の点に注意してください。

-   コネクタをインストールしたサーバーからブラウザーでツールを開く。

-   コネクタに適用されるプロキシまたはファイアウォールが、このページにも適用されるようにする。 これを設定するため、Internet Explorer で、**[設定]** -&gt; **[インターネット オプション]** -&gt; **[接続]** -&gt; **[LAN の設定]** の順に進みます。 このページに、[LAN にプロキシ サーバーを使用する] フィールドがあります。 このボックスをオンにし、[アドレス] フィールドにプロキシ アドレスを入力します。

## <a name="next-steps"></a>次の手順
[Azure AD アプリケーション プロキシ コネクタについて](application-proxy-connectors.md)

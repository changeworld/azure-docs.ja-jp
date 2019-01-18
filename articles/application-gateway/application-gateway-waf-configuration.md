---
title: Azure Application Gateway での Web アプリケーション ファイアウォール要求サイズ制限と除外リスト - Azure portal
description: この記事では、Azure portal を使用して構成する Application Gateway での Web アプリケーション ファイアウォール要求サイズ制限と除外リストについて説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.workload: infrastructure-services
ms.date: 11/6/2018
ms.author: victorh
ms.openlocfilehash: bed406f90c8da62919337c1fa9f30221b0ba8d90
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2018
ms.locfileid: "53752726"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Web アプリケーション ファイアウォール要求サイズ制限と除外リスト

Azure Application Gateway Web アプリケーション ファイアウォール (WAF) は、Web アプリケーションを保護します。 この記事では、WAF 要求サイズ制限と除外リストの構成について説明します。

## <a name="waf-request-size-limits"></a>WAF 要求サイズの制限

![要求サイズの制限](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Web アプリケーション ファイアウォールでは、ユーザーは下限と上限の範囲内に収まる要求サイズ制限を構成することができます。 次の 2 つのサイズ制限の構成を使用できます。

- 最大要求本文サイズ フィールドは KB で指定されます。このフィールドでは、ファイルのアップロードを除く、全体的な要求サイズの制限が制御されます。 このフィールドには、1 KB (最小値) から 128 KB (最大値) までの値を指定することができます。 要求本文のサイズの既定値は 128 KB です。
- ファイル アップロード制限フィールドは MB 単位で指定され、このフィールドでは、最大許容ファイル アップロード サイズが制御されます。 このフィールドには、1 MB (最小値) から 500 MB (最大値) までの値を指定することができます。 ファイル アップロード制限の既定値は 100 MB です。

WAF には、要求本文の検査を有効または無効にするための構成可能なノブも用意されています。 既定では、要求本文の検査は有効になっています。 要求本文の検査を無効にした場合、HTTP メッセージ本文の内容は WAF によって評価されません。 そのような場合、WAF ではヘッダー、cookie、および URI には WAF 規則が引き続き適用されます。 要求本文の検査を無効にすると、最大要求本文サイズ フィールドは適用できなくなり、設定することもできません。 要求本文の検査をオフにすると、128 KB を超えるメッセージを WAF に送信できるようになりますが、メッセージ本文の脆弱性が検査されません。

## <a name="waf-exclusion-lists"></a>WAF 除外リスト

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

WAF の除外リストを使用すると、ユーザーは WAF の評価から特定の要求属性を省略することができます。 一般的な例として、認証フィールドまたはパスワード フィールドにおいて使用される、Active Directory で挿入されたトークンが挙げられます。 このような属性には、WAF ルールに基づいて誤検知をトリガーする可能性がある特殊文字が含まれる傾向があります。 WAF 除外リストに属性を追加すると、いずれの構成済みのアクティブな WAF ルールによっても、その属性は考慮されなくなります。 除外リストはスコープ内でグローバルです。

除外リストには以下の属性を追加できます。

* 要求ヘッダー
* 要求 Cookie
* 要求本文

   * フォームのマルチパート データ
   * XML
   * JSON

要求ヘッダー、本文、cookie、またはクエリ文字列属性については完全一致を指定することも、必要に応じて、部分一致を指定することもできます。

次に、サポートされている一致条件演算子を示します。

- **Equals**:この演算子は完全一致の場合に使用されます。 たとえば、**bearerToken** という名前のヘッダーを選択した場合は、**bearerToken** として設定されるセレクターで equals 演算子を使用します。
- **Starts with**:この演算子は指定したセレクター値で始まるすべてのフィールドと一致します。
- **Ends with**:この演算子は指定したセレクター値で終わるすべての要求フィールドと一致します。
- **Contains**:この演算子は指定したセレクター値を含むすべての要求フィールドと一致します。

すべての場合に、照合で大文字と小文字は区別されず、正規表現はセレクターとして使用できません。

## <a name="next-steps"></a>次の手順

ご自分の WAF 設定を構成したら、その WAF ログを表示する方法を学習できます。 詳細については、[Application Gateway の診断](application-gateway-diagnostics.md#diagnostic-logging)に関するトピックを参照してください。
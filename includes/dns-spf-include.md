---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: b0b66a910e762b5c14abb661c966fdb21015544e
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868156"
---
Sender Policy Framework (SPF) レコードは、ドメイン名を使って電子メールを送信することができる電子メール サーバーを指定するために使用されます。 送信した電子メールが受信者に迷惑メールとして分類されないようにするには、SPF レコードを正しく構成することが重要です。

もともと DNS に関する RFC では、このようなシナリオに対応するレコード タイプとして、新しい SPF が導入されていました。 また、旧式のネーム サーバーに対応するために、TXT レコード タイプを使用して SPF レコードを指定することも許可されていました。 このあいまいさが原因で混乱が生じていましたが、[RFC 7208](https://datatracker.ietf.org/doc/html/rfc7208#section-3.1) によって解決されました。 RFC 7208 では、SPF レコードは TXT レコード タイプを使用して作成する必要があると規定されています。 また、SPF レコード タイプが非推奨であることも規定されています。

**SPF レコードは Azure DNS でもサポートされていますが、TXT レコード タイプを使用して作成する必要があります。** 廃止された SPF レコード タイプはサポートされていません。 [DNS ゾーン ファイルをインポートする](../articles/dns/dns-import-export.md)際、SPF レコード タイプを使用している SPF レコードは、TXT レコード タイプに変換されます。

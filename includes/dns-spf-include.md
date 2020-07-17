---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "67181806"
---
Sender Policy Framework (SPF) レコードは、ドメイン名を使って電子メールを送信することができる電子メール サーバーを指定するために使用されます。 送信した電子メールが受信者に迷惑メールとして分類されないようにするには、SPF レコードを正しく構成することが重要です。

もともと DNS に関する RFC では、このようなシナリオに対応するレコード タイプとして、新しい SPF が導入されていました。 また、旧式のネーム サーバーに対応するために、TXT レコード タイプを使用して SPF レコードを指定することも許可されていました。 このあいまいさが原因で混乱が生じていましたが、[RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1) によって解決されました。 RFC 7208 では、SPF レコードは TXT レコード タイプを使用して作成する必要があると規定されています。 また、SPF レコード タイプが非推奨であることも規定されています。

**SPF レコードは Azure DNS でもサポートされていますが、TXT レコード タイプを使用して作成する必要があります。** 廃止された SPF レコード タイプはサポートされていません。 [DNS ゾーン ファイルをインポートする](../articles/dns/dns-import-export.md)際、SPF レコード タイプを使用している SPF レコードは、TXT レコード タイプに変換されます。

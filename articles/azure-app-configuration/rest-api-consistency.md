---
title: Azure App Configuration REST API - 整合性
description: Azure App Configuration REST API を使用したリアルタイムの整合性を確保するためのリファレンス ページ
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 4f11e6edcd4bc128f815db7e93b00b72bf990ea8
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423848"
---
# <a name="real-time-consistency"></a>リアルタイムの整合性

一部の分散システムの性質上、要求間のリアルタイムの整合性は、暗黙的に適用するのが困難です。 解決策は、複数の **同期トークン** の形式でプロトコル サポートを許可することです。 同期トークンは省略可能です。

## <a name="initial-request"></a>最初の要求

異なるクライアント インスタンスと要求の間でリアルタイムの整合性を保証するには、省略可能な `Sync-Token` 要求/応答ヘッダーを使用します。

構文:

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|パラメーター|説明|
|--|--|
| `<id>` | トークン ID (不透明) |
| `<value>` | トークン値 (不透明)。 base64 でエンコードされた文字列が使用できます |
| `<sn>` | トークン シーケンス番号 (バージョン)。 数字が高いほど、同じトークンの新しいバージョンを意味します。 コンカレンシーとクライアント キャッシュが向上します。 トークンのバージョンが含まれているため、クライアントではトークンの最後のバージョンのみを使用することができます。 要求には必要ありません。 |

## <a name="response"></a>Response

サービスからの各応答には、`Sync-Token` ヘッダーが含まれます。

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>後続の要求

後続の要求では、指定された `Sync-Token` に対する **リアルタイム** で整合性の取れた応答が保証されます。

```http
Sync-Token: <id>=<value>
```

`Sync-Token` ヘッダーが要求から除外されている場合、サービスが内部的に落ち着くまでの短い期間 (最大数秒間) にキャッシュされたデータが、サービスによって返される可能性があります。 この動作により、読み取りの直前に変更が行われた場合に、読み取りに不整合が生じる可能性があります。

## <a name="multiple-sync-tokens"></a>複数の同期トークン

サーバーは、1 つの要求に対して複数の同期トークンを使用して応答する場合があります。 次の要求に対してリアルタイムの **整合性** を維持するには、クライアントは受信したすべての同期トークンで応答する必要があります。 RFC に従って、複数のヘッダー値をコンマで区切る必要があります。

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```

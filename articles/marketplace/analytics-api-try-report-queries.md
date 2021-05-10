---
title: レポート クエリの試行 API
description: この API を使用して、コマーシャル マーケットプレースの分析レポート用のレポート クエリを実行します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 0db212be06182128bbd8a3bf694a2f893ce82eae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583550"
---
# <a name="try-report-queries-api"></a>レポート クエリの試行 API

この API は、レポート クエリ ステートメントを実行します。 この API は、データが想定どおりであるかどうかを確認するためにパートナーが使用できるレコードを 10 件だけ返します。

> [!IMPORTANT]
> この API のクエリ実行タイムアウトは 100 秒です。 API の処理時間が 100 秒を超えていることがわかった場合は、クエリの構文が正しい可能性が高く、そうでない場合は 200 以外のエラー コードを受け取っている可能性があります。 クエリ構文が正しい場合は、実際のレポート生成が成功します。

**要求の構文**

| **方法** | **要求 URI** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery={query text}` |
|||

**要求ヘッダー**

| **Header** | **Type** | **説明** |
| --- | --- | --- |
| 承認 | string | 必須。 `Bearer <token>` という形式の Azure Active Directory (Azure AD) アクセス トークン |
| Content-Type | string | `Application/JSON` |
|||

**QueryParameter**

| **パラメーター名** | **Type** | **説明** |
| --- | --- | --- |
| `exportQuery` | string | 実行する必要があるレポート クエリの文字列 |
| `queryId` | string | 有効な既存のクエリ ID |
|||

**パス** **パラメーター**

なし

**要求ペイロード**

なし

**用語集**

None

**応答**

応答ペイロードは、次のように構成されます。

応答コード: 200、400、401、403、404、500

応答ペイロード: クエリ実行の上位 10 行

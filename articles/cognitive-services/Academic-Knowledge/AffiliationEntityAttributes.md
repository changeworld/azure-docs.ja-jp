---
title: Academic Knowledge API の所属エンティティの属性
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API で所属エンティティに使用できる属性について説明します。
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: 52dcccebe1a1cbab7a6afadeb6b543b34b8b1eb7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143922"
---
# <a name="affiliation-entity"></a>所属エンティティ

> [!NOTE]
> 次の属性は、所属エンティティに固有です。 (Ty = '5')

名前 | 説明 | 種類 | Operations
--- | --- | --- | ---
AfN | 所属の標準化名 |string |等しい
CC | 所属の引用の総数 |Int32        |なし  
DAfN | 所属の表示名 |string |なし
E | 拡張メタデータ</br></br>**重要**:この属性は非推奨となっており、レガシ アプリケーション用としてのみサポートされています。 この属性を個別に要求すると (つまり、属性 = Id、Ti、E)、すべての拡張メタデータ属性が、"*シリアル化された JSON 文字列*" で返されます。</br></br>拡張メタデータに含まれるすべての属性が最上位レベルの属性として使用できるようになり、そのように要求できるようになりました (つまり、属性 = Id、Ti、DOI、IA)。 | [拡張](#extended) | なし
ECC | 所属の引用の推定総数 |Int32 |なし
Id | エンティティ ID |Int64 |等しい
PC | このエンティティとの関係で書き込まれた発行総数 | Int32 | なし

## <a name="extended"></a>拡張

> [!IMPORTANT]
> この属性は非推奨となっており、レガシ アプリケーション用としてのみサポートされています。 この属性を個別に要求すると (つまり、属性 = Id、Ti、E)、すべての拡張メタデータ属性が、"*シリアル化された JSON 文字列*" で返されます。</br></br>拡張メタデータに含まれるすべての属性が最上位レベルの属性として使用できるようになり、そのように要求できるようになりました (つまり、属性 = Id、Ti、DOI、IA)。

> [!IMPORTANT]
> 個々の拡張属性を、"E." スコープを使用して要求すること、つまり "E.DN" のサポートは、非推奨となる予定です。 これは技術的にはまだサポートされていますが、個々の拡張属性を、"E." スコープを使用して要求すると、属性値が JSON 応答の 2 つの場所で ("E" オブジェクトの一部として、および最上位レベルの属性として) 返されます。

名前 | 説明 | 種類 | Operations
--- | --- | --- | ---
PC | このエンティティとの関係で書き込まれた発行総数 | Int32 | なし

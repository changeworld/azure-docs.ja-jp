---
title: 作成者エンティティの属性 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API で作成者エンティティに使用できる属性について説明します。
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: d5fc770c380397f605f8810fa41d3a8907f2358e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146496"
---
# <a name="author-entity"></a>著者エンティティ

> [!NOTE]
> 次の属性は、著者エンティティに固有です。 (Ty = '1')

名前 | 説明 | 種類 | Operations
--- | --- | --- | ---
Id      | エンティティ ID                             |Int64      |等しい
AuN     | 著者の標準化名                    |string     |等しい
CC      | 著者の引用の総数           |Int32      |なし  
DAuN    | 著者の表示名                   |string     |なし
E | 拡張メタデータ</br></br>**重要**:この属性は非推奨となっており、レガシ アプリケーション用としてのみサポートされています。 この属性を個別に要求すると (つまり、属性 = Id、Ti、E)、すべての拡張メタデータ属性が、"*シリアル化された JSON 文字列*" で返されます。</br></br>拡張メタデータに含まれるすべての属性が最上位レベルの属性として使用できるようになり、そのように要求できるようになりました (つまり、属性 = Id、Ti、DOI、IA)。 | [拡張](#extended) | なし
ECC     | 著者の引用の推定総数 |Int32      |なし
LKA.AfId | 作成者に対して見つかった最後の既知の所属のエンティティ ID | Int64 | なし
LKA.AfN | 作成者に対して見つかった最後の既知の所属の標準化名 | string | なし
PC | 作成者の発行総数 | Int32 | なし

## <a name="extended"></a>拡張

> [!IMPORTANT]
> この属性は非推奨となっており、レガシ アプリケーション用としてのみサポートされています。 この属性を個別に要求すると (つまり、属性 = Id、Ti、E)、すべての拡張メタデータ属性が、"*シリアル化された JSON 文字列*" で返されます。</br></br>拡張メタデータに含まれるすべての属性が最上位レベルの属性として使用できるようになり、そのように要求できるようになりました (つまり、属性 = Id、Ti、DOI、IA)。

> [!IMPORTANT]
> 個々の拡張属性を、"E." スコープを使用して要求すること、つまり "E.DN" のサポートは、非推奨となる予定です。 これは技術的にはまだサポートされていますが、個々の拡張属性を、"E." スコープを使用して要求すると、属性値が JSON 応答の 2 つの場所で ("E" オブジェクトの一部として、および最上位レベルの属性として) 返されます。

名前 | 説明 | 種類 | Operations
--- | --- | --- | ---
LKA.AfId | 作成者に対して見つかった最後の既知の所属のエンティティ ID | Int64 | なし
LKA.AfN | 作成者に対して見つかった最後の既知の所属の標準化名 | string | なし
PC | 作成者の発行総数 | Int32 | なし

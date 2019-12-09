---
title: 会議 (インスタンス) エンティティの属性 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API で会議 (インスタンス) エンティティに使用できる属性について説明します。
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: c6761206a58724dae96d9dc6f6234658892d4d18
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146566"
---
# <a name="conference-instance-entity"></a>会議 (インスタンス) エンティティ

> [!NOTE]
> 次の属性は、会議 (インスタンス) エンティティに固有です。 (Ty = '4')

名前 | 説明 | 種類 | Operations
--- | --- | --- | ---
CC      |会議 (インスタンス) の引用の総数           |Int32      |なし  
CD.D    |会議 (インスタンス) イベントの日付    |Date       |Equals、IsBetween
CD.T    |会議 (インスタンス) イベントのタイトル   |Date       |Equals、IsBetween
CIARD   |会議 (インスタンス) の要約登録期限  |Date       |Equals、IsBetween
CIED    |会議 (インスタンス) の終了日    |Date       |Equals、IsBetween
CIFVD   |会議 (インスタンス) の最終バージョンの期限  |Date       |Equals、IsBetween
CIL     |会議 (インスタンス) の場所    |string     |Equals、StartsWith
CIN     |会議 (インスタンス) の標準化名 |string        |等しい
CINDD   |会議 (インスタンス) の通知日   |Date       |Equals、IsBetween
CISD    |会議 (インスタンス) の開始日  |Date       |Equals、IsBetween
CISDD   |会議 (インスタンス) の提出期限     |Date       |Equals、IsBetween
DCN     |会議 (インスタンス) の表示名  |string      |なし
E | 拡張メタデータ</br></br>**重要**:この属性は非推奨となっており、レガシ アプリケーション用としてのみサポートされています。 この属性を個別に要求すると (つまり、属性 = Id、Ti、E)、すべての拡張メタデータ属性が、"*シリアル化された JSON 文字列*" で返されます。</br></br>拡張メタデータに含まれるすべての属性が最上位レベルの属性として使用できるようになり、そのように要求できるようになりました (つまり、属性 = Id、Ti、DOI、IA)。 | [拡張](#extended) | なし
ECC     |会議 (インスタンス) の引用の推定総数 |Int32      |なし
FN | 会議 (インスタンス) のフル ネーム | string | なし
Id      |エンティティ ID                              |Int64      |等しい
PC | 会議 (インスタンス) の発行総数 | Int32 | なし
PCS.CN  |インスタンスの親会議 (シリーズ) の名前 |string  |等しい
PCS.CId |インスタンスの親会議 (シリーズ) の ID |Int64     |等しい

## <a name="extended"></a>拡張

> [!IMPORTANT]
> この属性は非推奨となっており、レガシ アプリケーション用としてのみサポートされています。 この属性を個別に要求すると (つまり、属性 = Id、Ti、E)、すべての拡張メタデータ属性が、"*シリアル化された JSON 文字列*" で返されます。</br></br>拡張メタデータに含まれるすべての属性が最上位レベルの属性として使用できるようになり、そのように要求できるようになりました (つまり、属性 = Id、Ti、DOI、IA)。

> [!IMPORTANT]
> 個々の拡張属性を、"E." スコープを使用して要求すること、つまり "E.DN" のサポートは、非推奨となる予定です。 これは技術的にはまだサポートされていますが、個々の拡張属性を、"E." スコープを使用して要求すると、属性値が JSON 応答の 2 つの場所で ("E" オブジェクトの一部として、および最上位レベルの属性として) 返されます。

名前 | 説明 | 種類 | Operations
--- | --- | --- | ---
FN | 会議 (インスタンス) のフル ネーム | string | なし
PC | 会議 (インスタンス) の発行総数 | Int32 | なし

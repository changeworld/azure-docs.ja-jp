---
title: Academic Knowledge API の会議 (インスタンス) エンティティの属性 | Microsoft Docs
description: Cognitive Services の Academic Knowledge API で会議 (インスタンス) エンティティに使用できる属性について説明します。
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: ef2bca4346a4666905f3dfb7bd448720f3b0ef8b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372757"
---
# <a name="conference-instance-entity"></a>会議 (インスタンス) エンティティ

<sub> *次の属性は、会議 (インスタンス) エンティティに固有です。(Ty = '4') </sub>

Name    |説明                            |type       | [操作]
------- | ------------------------------------- | --------- | ----------------------------
ID      |エンティティ ID                              |Int64      |等しい
CIN     |会議 (インスタンス) の標準化名 ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |String     |等しい
DCN     |会議 (インスタンス) の表示名 ({ConferenceSeriesName} : {ConferenceInstanceYear})       |String     |なし
CIL     |会議 (インスタンス) の場所    |String     |Equals、<br/>StartsWith
CISD    |会議 (インスタンス) の開始日  |日付       |Equals、<br/>IsBetween
CIED    |会議 (インスタンス) の終了日    |日付       |Equals、<br/>IsBetween
CIARD   |会議 (インスタンス) の要約登録期限  |日付       |Equals、<br/>IsBetween
CISDD   |会議 (インスタンス) の提出期限     |日付       |Equals、<br/>IsBetween
CIFVD   |会議 (インスタンス) の最終バージョンの期限  |日付       |Equals、<br/>IsBetween
CINDD   |会議 (インスタンス) の通知日   |日付       |Equals、<br/>IsBetween
CD.T    |会議 (インスタンス) イベントのタイトル   |日付       |Equals、<br/>IsBetween
CD.D    |会議 (インスタンス) イベントの日付    |日付       |Equals、<br/>IsBetween
PCS.CN  |インスタンスの会議 (シリーズ) の名前 |String     |等しい
PCS.CId |インスタンスの会議 (シリーズ) の ID |Int64    |等しい
CC      |会議 (インスタンス) の引用の総数           |Int32      |なし  
ECC     |会議 (インスタンス) の引用の推定総数 |Int32      |なし


## <a name="extended-metadata-attributes"></a>拡張メタデータの属性 ##

Name    | 説明               
--------|---------------------------    
FN      | 会議 (インスタンス) のフル ネーム
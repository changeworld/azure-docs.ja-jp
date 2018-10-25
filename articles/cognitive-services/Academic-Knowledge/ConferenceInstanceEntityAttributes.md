---
title: 会議 (インスタンス) エンティティの属性 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API で会議 (インスタンス) エンティティに使用できる属性について説明します。
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 6111ad00044943f12b2e098c4fd07ffb40185799
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902414"
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
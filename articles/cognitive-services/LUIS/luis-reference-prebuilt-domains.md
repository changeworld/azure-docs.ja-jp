---
title: 事前構築済みのドメインのリファレンス
titleSuffix: Azure
description: 事前構築済みのドメインのリファレンスです。事前構築済みのドメインは、Language Understanding Intelligent Service (LUIS) の意図とエンティティの事前構築済みのコレクションです。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: b615185472ede25fd6ceef9cc3e7325375e16ac7
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846677"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>LUIS アプリの事前構築済みのドメインのリファレンス
このリファレンスは、[事前構築済みのドメイン](luis-how-to-use-prebuilt-domains.md)に関する情報を提供します。事前構築済みのドメインは、LUIS が提供している意図とエンティティの事前構築済みのコレクションです。

対照的に、[カスタム ドメイン](luis-how-to-start-new-app.md)は意図とモデルがない状態から始まります。 任意の事前構築済みのドメインの意図とエンティティをカスタム モデルに追加できます。

# <a name="supported-domains-across-cultures"></a>カルチャ全体でサポートされているドメイン

サポートされているカルチャは英語のみです。 

<!--

The table below summarizes the currently supported domains. Support for English is usually more complete than others.


| Entity Type       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| Calendar    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -      | -    | -    | -     | -  |
| Communication   | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Email           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| HomeAutomation           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Notes      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Places    | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| RestaurantReservation   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo_IPA        | ✓    | ✓       | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Utilities          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Weather        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Web    | ✓    | -        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
||||||||||||| 

-->

<br><br>

|エンティティの種類|description|
|--|--|
|Calendar|Calendar では、個人的な会合や予定に関することを扱います。パブリック イベント (ワールド カップのスケジュールやシアトルのイベント カレンダーなど)、一般的なカレンダー (曜日や立秋、労働者の日など) では_ありません_。|
|Communication|電話をかける、SMS やインスタント メッセージを送信する、連絡を検索して追加するなど、コミュニケーション (通常は発信) に関連するさまざまな要求を行います。 連絡先の名前のみの問い合わせは、Communication ドメインには該当しません。|
|Email|Email は Communication ドメインのサブドメインです。 主に、電子メール経由でメッセージを送受信する要求が含まれています。|
|HomeAutomation|HomeAutomation ドメインには、スマート ホーム デバイスの制御に関する意図とエンティティが用意されています。 主に照明やエアコンに関連する制御コマンドをサポートしていますが、他の電気機器のための汎化的な機能も備えています。|
|メモ|Note ドメインには、ユーザーのためにメモを作成して項目を記述するための意図とエンティティが用意されています。|
|場所|Places には、会社、施設、レストラン、公共の場所、住所などが含まれます。 このドメインでは、場所の検索や、公共の場所の情報 (所在地、営業時間、距離など) についての質問がサポートされています。|
|RestaurantReservation|RestaurantReservation ドメインでは、レストランの予約を処理するための意図がサポートされています。|
|ToDo|ToDo ドメインには、ユーザーが ToDo 項目を追加、マーク、削除できる各種タスク リストが用意されています。|
|Utilities|Utilities ドメインは、すべての LUIS 事前構築済みモデル間の一般的なドメインで、さまざまなシナリオに共通の意図と発話が含まれています。|
|Weather|Weather ドメインでは、場所や時間を指定して気象条件や注意報を調べたり、気象条件別に時間を調べたりすることに重点を置いています。|
|Web|Web ドメインには、Web サイトを検索するための意図とエンティティが用意されています。|

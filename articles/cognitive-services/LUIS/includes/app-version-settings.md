---
title: インクルード ファイル
ms.topic: include
ms.custom: include file
ms.date: 5/04/2020
ms.openlocfilehash: 819dfa3127eb91a2f08687a76ea0586439b650a4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590990"
---
|Level|UI 設定|API 設定|Information|
|--|--|--|--|
|アプリ|Make endpoints public\(エンドポイントをパブリックにする\)|`Public`|予測キーがあり、アプリ ID を把握していれば、だれでもパブリック アプリにアクセスできます。 |
|Version|Use non-deterministic training\(非決定的トレーニングを使用する\)|`UseAllTrainingData`|トレーニングでは、ネガティブ サンプリングのごく一部を使用します。 小規模なネガティブ サンプリングではなくすべてのデータを使用する場合は、`true` に設定します。 |
|Version|Normalize diacritics\(分音記号を正規化する\)|`NormalizeDiacritics`|分音記号を正規化すると、発話内の分音記号を持つ文字が通常の文字に置き換えられます。|
|Version|Normalize punctuation\(句読点を正規化する\)|`NormalizePunctuation`|句読点を正規化すると、モデルがトレーニングされる前、およびエンドポイント クエリが予測される前に、発話から句読点が削除されます。|
|Version|Normalize word forms\(単語形式を正規化する\)|`NormalizeWordForm`|ルートを超えた単語形式を無視します。|

正規化の[概念](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation)と、[app](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58aeface39e2bb03dcd5909e) および [version](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API の使用方法を学習して、これらの設定を更新したり、LUIS ポータルの **[アプリケーション設定]** ページの **[管理]** セクションを使用したりします。
---
title: LUIS におけるデータ ストレージの概要 - Azure | Microsoft Docs
description: データを Language Understanding (LUIS) に格納する方法を説明します。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/08/2018
ms.author: v-geberr
ms.openlocfilehash: f235c787e7d2064696e5421219a297d914b5882d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266007"
---
# <a name="data-storage-and-removal"></a>データの保存と削除
LUIS では、キーによって指定された領域に対応する Azure のデータ ストアに、データが暗号化されて格納されます。 このデータは 30 日間保存されます。 

## <a name="export-and-delete-app"></a>アプリのエクスポートと削除
ユーザーは、アプリの[エクスポート](create-new-app.md#export-app)と[削除](create-new-app.md#delete-app)を完全に制御できます。 

## <a name="utterances-in-an-intent"></a>意図における発話
[LUIS][LUIS] のトレーニングに使用した発話の例は削除します。 LUIS アプリから発話の例を削除すると、LUIS Web サービスから削除されて、エクスポートに使用できなくなります。

## <a name="utterances-in-review"></a>確認中の発話
**[[Review endpoint utterances]\(エンドポイントの発話の確認\)](label-suggested-utterances.md)** ページで LUIS が提案するユーザー発話のリストから、発話を削除できます。 このリストから削除した発話は提案されなくなりますが、ログからは削除されません。

## <a name="accounts"></a>アカウント
アカウントを削除すると、発話例およびログと共に、すべてのアプリが削除されます。 データが 60 日間保持された後、アカウントとデータは完全に削除されます。

アカウントの削除は、**[設定]** ページで実行できます。 右上部のナビゲーション バーにあるアカウント名を選択して、**[設定]** ページに移動します。

## <a name="data-inactivity-as-an-expired-subscription"></a>有効期限が切れたサブスクリプションでの非アクティブなデータ
データの保有と削除の目的で、非アクティブの LUIS アプリは、"_Microsoft の裁量_" で有効期限が切れたサブスクリプションとして扱われることがあります。 過去 90 日間に次の条件を満たすアプリは、非アクティブと見なされます。 

* 呼び出しが行われて**いない**
* 変更されていない
* 最新のキーが割り当てられていない
* ユーザーがサインインしていない

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [アプリのエクスポートと削除について確認します](create-new-app.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
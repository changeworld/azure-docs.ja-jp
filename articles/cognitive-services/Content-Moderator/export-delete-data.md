---
title: Content Moderator のデータをエクスポートするか削除する - Azure Cognitive Services | Microsoft Docs
description: Content Moderator のデータをエクスポートまたは削除する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 05/25/2018
ms.author: v-jaswel
ms.openlocfilehash: 1dbb645a033c6db5ffa9003a53f30fd927131298
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35378061"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Content Moderator のユーザー データをエクスポートまたは削除する

Content Moderator は、サービスの運用にユーザー データを収集しますが、データの参照、エクスポート、削除は、ユーザーが [[Review UI]\(UI の確認\)](http://contentmoderator.cognitive.microsoft.com/) と [API](https://docs.microsoft.com/en-us/azure/cognitive-services/content-moderator/api-reference) を使用して完全に制御できます。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Content Moderator のユーザー データをエクスポートおよび削除する方法については、次の表を参照してください。

| データ | エクスポート操作 | 削除操作。 |
| ---- | ---------------- | ---------------- |
| アカウントの情報 (サブスクリプション キー) | 該当なし | Azure Portal (Azure のサブスクリプション) を使用して削除します。 または、[[Review UI]\(UI の確認\)](http://contentmoderator.cognitive.microsoft.com/) チーム設定ページの **[チームの削除]** ボタンを使用します。 |
| カスタム照合用の画像 | [画像 ID を取得します](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676)。 画像は、一方向の独自のハッシュ形式で格納され、実際の画像を抽出する方法はありません。 | [すべての画像を削除します](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686)。 または、Azure Portal を使用して、Content Moderator のリソースを削除します。 |
| カスタム照合の用語 | [すべての用語を取得します](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | [すべての用語を削除します](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d)。 または、Azure Portal を使用して、Content Moderator のリソースを削除します。 |
| タグ | 該当なし | [Review UI Tag]\(UI タグの確認\) 設定ページの **[削除]** アイコンを使用します。 または、[[Review UI]\(UI の確認\)](http://contentmoderator.cognitive.microsoft.com/) チーム設定ページの **[チームの削除]** ボタンを使用します。 |
| レビュー | [レビューを取得します](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | [[Review UI]\(UI の確認\)](http://contentmoderator.cognitive.microsoft.com/) チーム設定ページの **[チームの削除]** ボタンを使用します。
| ユーザー | 該当なし | [[Review UI]\(UI の確認\)](http://contentmoderator.cognitive.microsoft.com/) チーム設定ページに各ユーザー向けに用意されている **[削除]** アイコンを使用します。 または、[[Review UI]\(UI の確認\)](http://contentmoderator.cognitive.microsoft.com/) チーム設定ページの **[チームの削除]** ボタンを使用します。 |


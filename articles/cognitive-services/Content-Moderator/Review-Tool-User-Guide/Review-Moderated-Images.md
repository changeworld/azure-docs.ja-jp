---
title: タグ付きの画像をレビューする - Content Moderator
titlesuffix: Azure Cognitive Services
description: レビュー ツールで、モデレート担当者が Web ポータル内の画像をレビューできる方法を説明します。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: e096e65e3016f33361f772a75ab8f71603970a5f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58096607"
---
# <a name="let-human-reviewers-review-images"></a>人間のレビュー担当者に画像をレビューさせます

Content Moderation にサインアップし、サブスクリプション キーを入手したら、画像のレビュー機能を試すことができます。

1. [目視レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)を開き、サインインします。 
2. [Try]\(試す\) タブをクリックし、レビューするいくつかの画像をアップロードします。
3. [確認] タブをクリックし、画像を選択します。

   ![Review Image オプションが強調表示されたレビュー ツールを示す Chrome ブラウザー](images/review-images-1.png)

   レビュー ツールによって割り当てられているラベルと共に画像が表示されます。 レビュー中の画像は、チームの他のレビュー担当者が使用できません。

4. "表示するレビュー" のスライダー (1) を動かして、画面に表示される画像の数を調整します。 タグ付きまたはタグなしのボタン (2) をクリックして、画像をそれに応じて並べ替えます。 タグ (3) をクリックして、オンとオフを切り替えます。

   ![レビュー用のタグが付いた画像とレビュー ツールが表示された Chrome ブラウザー](images/review-images-2.png)
 
5. 画像の詳細を表示するには、サムネイル上の省略記号をクリックし、続いて **[詳細の表示]** オプションをクリックします。 画像をサブチームに割り当てるには、**[Move to]\(移動先\)** オプションを選択します。
 
   ![[詳細の表示] オプションが強調表示された画像](images/review-images-3.png)

6. 詳細ページで画像モデレーション情報を参照します。

   ![別のウィンドウにモデレーションの詳細が一覧表示された画像](images/review-images-4.png)
 
7. タグの割り当てをレビューし、必要に応じて更新したら、**[次へ]** をクリックしてレビューを送信します。

送信後、約 5 秒間は **[前へ]** をクリックして前の画面に戻り、画像をもう一度レビューできます。 その後、画像は送信キューになくなり、**[前へ]** ボタンは使用できなくなります。

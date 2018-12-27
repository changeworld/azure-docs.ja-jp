---
title: Azure Content Moderator での画像のレビュー | Microsoft Docs
description: このレビュー ツールでは、モデレート担当者が画像をレビューできます。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/06/2017
ms.author: sajagtap
ms.openlocfilehash: 97879747bbfde2247e41847d8d68bf7f19530ecb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372781"
---
# <a name="review-moderated-images"></a>モデレートされた画像のレビュー

Content Moderation にサインアップし、サブスクリプション キーを入手したら、画像のレビュー機能を試すことができます。

1.  [目視レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)を開き、サインインします。 
2.  [Try]\(試す\) タブをクリックし、レビューするいくつかの画像をアップロードします。
3.  [確認] タブをクリックし、画像を選択します。

  ![画像のレビュー オプション](images/review-images-1.png)

  レビュー ツールによって割り当てられているラベルと共に画像が表示されます。 レビュー中の画像は、チームの他のレビュー担当者が使用できません。

4.  "表示するレビュー" のスライダー (1) を動かして、画面に表示される画像の数を調整します。 タグ付きまたはタグなしのボタン (2) をクリックして、画像をそれに応じて並べ替えます。 タグ (3) をクリックして、オンとオフを切り替えます。

  ![画像のレビューの参照](images/review-images-2.png)
 
5.  画像の詳細を表示するには、サムネイル上の省略記号をクリックし、続いて **[詳細の表示]** オプションをクリックします。 画像をサブチームに割り当てるには、**[Move to]\(移動先\)** オプションを選択します。
 
  ![[詳細の表示] および [Move to]\(移動先\) オプション](images/review-images-3.png)

6. 詳細ページで画像モデレーション情報を参照します。

  ![画像の詳細を表示](images/review-images-4.png)
 
7.  タグの割り当てをレビューし、必要に応じて更新したら、**[次へ]** をクリックしてレビューを送信します。

送信後、約 5 秒間は **[前へ]** をクリックして前の画面に戻り、画像をもう一度レビューできます。 その後、画像は送信キューになくなり、**[前へ]** ボタンは使用できなくなります。

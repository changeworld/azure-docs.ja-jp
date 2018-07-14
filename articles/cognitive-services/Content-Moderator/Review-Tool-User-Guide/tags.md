---
title: Azure Content Moderator でのタグの使用 | Microsoft Docs
description: Content Moderator には既定のタグが含まれており、ビジネスに固有のコンテンツをモデレートするためのカスタム タグを作成することができます。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: add4c685c07c63944ae89f48a47ac78df28c1623
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372925"
---
# <a name="about-tags"></a>タグについて #

2 つの既定のタグ (a – isadult と r – isracy) に加えて、よりターゲットを絞ったスキャンを行うのためのカスタム タグを作成できます。 これらのカスタム タグは、レビュー担当者が画像またはテキストに割り当てることができます。

## <a name="create-tags"></a>タグの作成 ##

1.  [設定] タブから [タグ] を選択します。

  ![コンテンツ モデレーション タグ](images/tags-1.png)

2.  タグの 2 文字の短いコードを入力します。
3.  タグの名前を入力します。 短くてわかりやすい名前にします。 たとえば、"isNudity" などです。
4.  説明を入力します。
5.  [追加] をクリックします。
6.  タグの作成が完了したら、[保存] をクリックします。

![コンテンツ モデレーション タグの定義](images/tags-2-define.png)

## <a name="using-custom-tags"></a>カスタム タグの使用 ##

カスタム タグは、目視レビュー中に使用されます。 プレビューで表示され、レビュー担当者がクリックして選択します。

![コンテンツ モデレーション タグの使用](images/tags-3-use.png)

[Is Visible]\(表示\) をオンまたはオフにして、さまざまなレビューに対してさまざまなタグをオフにすることができます。
 
![コンテンツ モデレーション タグの無効化](images/tags-4-disable.png)

2 つの既定のタグ (isadult および isracy) は削除できませんが、自分で定義したカスタム タグは削除できます。 削除するタグの横にあるゴミ箱をクリックします。

![コンテンツ モデレーション タグの削除](images/tags-5-delete.png)

## <a name="next-steps"></a>次の手順 ##

画像のモデレーションにタグを使用する方法については、[モデレートされた画像をレビューする方法](Review-Moderated-Images.md)に関する記事をご覧ください。

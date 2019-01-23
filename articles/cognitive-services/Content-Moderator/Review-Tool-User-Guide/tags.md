---
title: コンテンツ モデレーションにカスタム タグを使用する - Content Moderator
titlesuffix: Azure Cognitive Services
description: Content Moderator には既定のタグが含まれており、ビジネスに固有のコンテンツをモデレートするためのカスタム タグを作成することができます。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: c1a547f99995d25d19dafb03276306c50a544c9a
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264718"
---
# <a name="create-and-use-moderation-tags"></a>モデレーション タグを作成して使用する

2 つの既定のタグ **isadult** (**a**) および **isracy** (**r**) に加えて、よりターゲットを絞ったスキャンを行うのためのカスタム タグを作成できます。 これらのカスタム タグは、レビュー担当者が画像またはテキストに割り当てることができます。

## <a name="create-tags"></a>タグの作成

1.  [設定] タブから [タグ] を選択します。

  ![コンテンツ モデレーション タグ](images/tags-1.png)

2.  タグの 2 文字の短いコードを入力します。
3.  タグの名前を入力します。 短くてわかりやすい名前にします。 たとえば、**isbullying** などとします。
4.  説明を入力します。
5.  [追加] をクリックします。
6.  タグの作成が完了したら、[保存] をクリックします。

![コンテンツ モデレーション タグの定義](images/tags-2-define.png)

## <a name="using-custom-tags"></a>カスタム タグの使用

カスタム タグは、目視レビュー中に使用されます。 プレビューで表示され、レビュー担当者がクリックして選択します。

![コンテンツ モデレーション タグの使用](images/tags-3-use.png)

[Is Visible]\(表示\) をオンまたはオフにして、さまざまなレビューに対してさまざまなタグをオフにすることができます。
 
![コンテンツ モデレーション タグの無効化](images/tags-4-disable.png)

2 つの既定のタグ (**isadult** および **isracy**) は削除できませんが、自分で定義したカスタム タグは削除できます。 削除するタグの横にあるゴミ箱をクリックします。

![コンテンツ モデレーション タグの削除](images/tags-5-delete.png)

## <a name="next-steps"></a>次の手順

画像のモデレーションにタグを使用する方法については、「[モデレートされた画像のレビュー](Review-Moderated-Images.md)」をご覧ください。

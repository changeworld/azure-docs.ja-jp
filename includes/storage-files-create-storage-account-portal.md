---
title: storage-files-create-storage-account-portal
description: Azure Files 用のストレージ アカウントを作成します。
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33159ab6dd014a153c8fd317fd291aeca033d6e9
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717837"
---
ストレージ アカウントは、Azure ファイル共有またはその他のストレージ リソース (BLOB やキューなど) をデプロイできるストレージの共有プールです。 1 つのストレージ アカウントに格納できる共有の数に制限はありません。 1 つの共有に格納できるファイルの数に制限はなく、ストレージ アカウントの容量の上限までファイルを格納できます。

ストレージ アカウントを作成するには、以下の手順を実行します。

1. 左側のメニューで、 **[+]** を選択してリソースを作成します。
1. **[ストレージ アカウント]** を選択して、ストレージ アカウントを作成します。

    :::image type="content" source="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png" alt-text="[リソースの作成] ブレードの [ストレージ アカウント] オプションのスクリーンショット。" lightbox="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png":::

1. **[名前]** に「*mystorageacct*」と入力し、それに続けて、一意の名前であることを示す緑色のチェック マークが表示されるまでランダムな数字をいくつか入力します。 ストレージ アカウント名はすべて小文字で、グローバルに一意である必要があります。 ストレージ アカウント名をメモしておきます。 これは後で使用します。 
1. **[パフォーマンス]** では、既定値の **[Standard]** を選択したままにします。
1. **[レプリケーション]** で **[ローカル冗長ストレージ (LRS)]** を選択します。
1. **[サブスクリプション]** で、ストレージ アカウントを作成するときに使用したサブスクリプションを選択します。 利用しているサブスクリプションが 1 つだけの場合は、それが既定値となります。
1. **[リソース グループ]** で **[新規作成]** を選択します。 名前として、「*myResourceGroup*」と入力します。
1. **[場所]** で **[米国東部]** を選択します。
1. 終わったら、 **[作成]** を選択してデプロイを開始します。
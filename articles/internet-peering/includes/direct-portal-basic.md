---
title: インクルード ファイル
titleSuffix: Azure
description: インクルード ファイル
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 79cf4b2edd1a25df427cf15f9ee7f2f331ebd2df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75773770"
---
1. **[リソースの作成]**  >  **[すべて表示]** をクリックします。

    > [!div class="mx-imgBorder"]
    > ![ピアリングの検索](../media/setup-seeall.png)

1. 検索ボックスで *[ピアリング]* を検索し、キーボードの *Enter* キーを押します。 結果から、 **[ピアリング]** リソースをクリックします。

    > [!div class="mx-imgBorder"]
    > ![ピアリングの起動](../media/setup-launch.png)

1. **[ピアリング]** を起動したら、詳細を理解するためにページを確認します。 準備ができたら、 **[作成]** をクリックします。

    > [!div class="mx-imgBorder"]
    > ![ピアリングを作成する](../media/setup-create.png)

1. **[ピアリングの作成]** ページの **[基本]** タブで、次のようにフィールドに入力します。

    > [!div class="mx-imgBorder"]
    > ![ピアリングの基本](../media/setup-basics-tab.png)

    * Azure **サブスクリプション**を選択します。
    * **[リソース グループ]** では、ドロップダウンから既存のリソース グループを選択するか、 **[新規作成]** をクリックして新しいグループを作成します。 この例では、新しいリソース グループを作成します。
    * **[名前]** は、リソース名に対応し、任意の名前を選択できます。
    * 前の手順で既存のリソース グループを選択した場合は、 **[リージョン]** が自動的に選択されます。 新しいリソース グループを作成することを選択した場合は、リソースが配置される Azure リージョンも選択する必要があります。 米国東部

        > [!NOTE]
        > リソース グループが配置されるリージョンは、Microsoft とのピアリングを作成する場所とは関係ありません。 ただし、最も近い Azure リージョンに存在するリソース グループ内のピアリング リソースを整理することをお勧めします。 例: Ashburn のピアリングでは、*米国東部*または*米国東部 2* にリソース グループを作成できます

    * **[ピア ASN]** フィールドで、ASN を選択します。

        > [!IMPORTANT]
        > * ピアリング要求を送信する前には、ValidationState が "承認済み" になっている ASN だけを選択できます。 PeerAsn 要求を送信したばかりの場合は、ASN アソシエーションが "承認済み" になるまで、12 時間ほど待ちます。 選択した ASN が検証待ちの場合は、エラー メッセージが表示されます。 
        > * 選択する必要がある ASN が表示されない場合は、適切なサブスクリプションを選択しているかどうかを確認します。 そのようにしている場合は、[[ピア ASN を Azure サブスクリプションに関連付ける]](../howto-subscription-association-portal.md) を使用して、PeerAsn を既に作成したかどうかを確認します。

        > [!div class="mx-imgBorder"]
        > ![ピアリングの基本の入力](../media/setup-direct-basics-filled-tab.png)

    * **[次へ: 構成]** をクリックして、続行します。

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
ms.openlocfilehash: 632490498b8dd13414657edb9518cd543ac07af6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678861"
---
1. **[ピアリングの作成]** ページの **[構成]** タブで、ここに示されているようにボックスに入力します。

    > [!div class="mx-imgBorder"]
    > ![[ピアリングの作成] ページの [構成] タブ](../media/setup-direct-conf-tab.png)

    * **[ピアリングの種類]** には、 **[Direct]** を選択します。
    * **[Microsoft ネットワーク]** では、 **[AS8075]** を選択します。 [ASN 8069] は選択しないでください。 これは特定のアプリケーション用に予約されており、[Microsoft ピアリング](mailto:peering@microsoft.com)によってのみ使用されます。
    * **[SKU]** には、 **[Basic Free]\(基本無料\)** を選択します。 [Premium Free]\(プレミアム無料\) は、特定のアプリケーション用に予約されているため、選択しないでください。
    * ピアリングを Azure リソースに変換する **[Metro]\(都市圏\)** の場所を選択します。 選択した **[Metro]\(都市圏\)** の場所に、Azure リソースに変換されない Microsoft とのピアリング接続がある場合、それらの接続は、示されているように **[ピアリング接続]** セクションに一覧表示されます。 これで、それらのピアリング接続を Azure リソースに変換できるようになりました。

        > [!div class="mx-imgBorder"]
        > ![ピアリング接続リスト](../media/setup-directlegacy-conf-tab.png)

1. 帯域幅を更新する必要がある場合は、回線の [編集] ボタンを選択して、接続の設定を変更します。

    > [!div class="mx-imgBorder"]
    > ![[編集]](../media/setup-directlegacy-conf-tab-edit.png) ボタン

    > [!NOTE]
    > 選択した **[Metro]\(都市圏\)** の場所に、Microsoft とのピアリング接続を追加する場合は、 **[新規作成]** を選択します。 詳細については、「[ポータルを使用して Direct ピアリングを作成または変更する](../howto-direct-portal.md)」を参照してください。
    >

1. **[Review + create]\(レビュー + 作成\)** を選択します。 入力した情報の基本的な検証がポータルで実行されていることがわかります。 上部のリボンには、"*最終検証を実行しています...* " というメッセージが表示されます。

    > [!div class="mx-imgBorder"]
    > ![[ピアリングの検証] タブ](../media/setup-direct-review-tab-validation.png)

1. メッセージが "*検証に成功しました*" に変わったら、情報を確認します。 **[作成]** を選択して、要求を送信します。 要求を変更するには、 **[前へ]** を選択して、手順を繰り返します。

    > [!div class="mx-imgBorder"]
    > ![ピアリングの送信](../media/setup-direct-review-tab-submit.png)

1. 要求を送信したら、デプロイが完了するまで待ちます。 デプロイが失敗した場合は、[Microsoft ピアリング](mailto:peering@microsoft.com)にお問い合わせください。 次に示すように、正常なデプロイが表示されます。

    > [!div class="mx-imgBorder"]
    > ![ピアリング成功](../media/setup-direct-success.png)

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
ms.openlocfilehash: 268703081a2a40e8bcc665889eaeaf8edd673bfd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680940"
---
1. **[ピアリングの作成]** ページの **[構成]** タブで、次のようにボックスに入力します。

    > [!div class="mx-imgBorder"]
    > ![[ピアリングの作成] ページの [Exchange] のピアリングの種類](../media/setup-exchange-conf-tab.png)

    * **[ピアリングの種類]** には、 **[Exchange]** を選択します。
    * **[SKU]** には、 **[Basic Free]\(基本無料\)** を選択します。
    * ピアリングを設定する **[Metro]\(都市圏\)** の場所を選択します。

        > [!NOTE]
        > 選択した **[Metro]\(都市圏\)** の場所に Microsoft とのピアリング接続が既にあり、ポータルを使用して初めてその場所にピアリングを設定する場合は、示されているように、既存のピアリング接続が **[ピアリング接続]** セクションに一覧表示されます。 これらのピアリング接続は、Microsoft によって Azure リソースに自動的に変換されるので、それらすべてを新しい接続と共に 1 か所で管理できます。 詳細については、[ポータルを使用した従来の Exchange ピアリングの Azure リソースへの変換](../howto-legacy-exchange-portal.md)に関するページを参照してください。
        >

1. **[ピアリング接続]** で **[新規作成]** を選択し、設定する新しい接続ごとに 1 行を追加します。

    * 接続設定を構成または変更するには、行の [編集] ボタンを選択します。

        > [!div class="mx-imgBorder"]
        > ![[編集] ボタン](../media/setup-exchange-conf-tab-edit.png)

    * 行を削除するには、 **[...]**  >  **[削除]** を選択します。

        > [!div class="mx-imgBorder"]
        > ![[削除] ボタン](../media/setup-exchange-conf-tab-delete.png)

    * ここに示すように、接続のすべての設定を指定する必要があります。

         > [!div class="mx-imgBorder"]
         > ![Exchange ピアリング接続ページ](../media/setup-exchange-conf-tab-connection.png)

        1. 接続を設定する必要がある **[Peering facility]\(ピアリング ファシリティ\)** を選択します。
        1. **[IPv4 アドレス]** と **[IPv6 アドレス]** のボックスに、neighbor コマンドを使用して Microsoft ルーターに構成される IPv4 と IPv6 のアドレスをそれぞれ入力します。
        1. **[Maximum advertised IPv4 addresses]\(アドバタイズされる最大の IPv4 アドレス\)** と **[Maximum advertised IPv6 addresses]\(アドバタイズされる最大の IPv6 アドレス\)** のボックスそれぞれに、アドバタイズする IPv4 と IPv6 のプレフィックスの番号を入力します。
        1. **[OK]** を選択して接続設定を保存します。

1. 前に選択した **[Metro]\(都市圏\)** 内で、Microsoft がユーザーのネットワークと同一の場所に設置されている任意のファシリティに接続を追加するには、この手順を繰り返します。

1. 必要なすべての接続を追加したら、 **[確認と作成]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![ピアリングの [構成] タブ](../media/setup-exchange-conf-tab-final.png)

1. 入力した情報の基本的な検証がポータルで実行されていることがわかります。 上部のリボンには、"*最終検証を実行しています...* " というメッセージが表示されます。

    > [!div class="mx-imgBorder"]
    > ![[ピアリングの検証] タブ](../media/setup-direct-review-tab-validation.png)

1. メッセージが "*検証に成功しました*" に変わったら、情報を確認します。 **[作成]** を選択して、要求を送信します。 要求を変更するには、 **[前へ]** を選択して、手順を繰り返します。

    > [!div class="mx-imgBorder"]
    > ![ピアリングの送信](../media/setup-exchange-review-tab-submit.png)

1. 要求を送信したら、デプロイが完了するまで待ちます。 デプロイが失敗した場合は、[Microsoft ピアリング](mailto:peering@microsoft.com)にお問い合わせください。 次に示すように、正常なデプロイが表示されます。

    > [!div class="mx-imgBorder"]
    > ![ピアリング成功](../media/setup-direct-success.png)

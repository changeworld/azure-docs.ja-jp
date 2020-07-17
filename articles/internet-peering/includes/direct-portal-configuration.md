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
ms.openlocfilehash: fda22346a44388248e37473bc7891b8a130569c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681060"
---
1. **[ピアリングの作成]** ページの **[構成]** タブで、ここに示されているようにボックスに入力します。

    * **[ピアリングの種類]** には、 **[Direct]** を選択します。
    * **[Microsoft ネットワーク]** では、 **[AS8075]** を選択します。 ASN 8069 を使用してピアリングを作成しないでください。 これは特定のアプリケーション用に予約されており、[Microsoft ピアリング](mailto:peering@microsoft.com)によってのみ使用されます。
    * **[SKU]** には、 **[Basic Free]\(基本無料\)** を選択します。 [Premium Free]\(プレミアム無料\) は、特定のアプリケーション用に予約されているため、選択しないでください。
    * ピアリングを設定する **[Metro]\(都市圏\)** の場所を選択します。

        > [!NOTE]
        > 選択した **[Metro]\(都市圏\)** の場所に Microsoft とのピアリング接続が既にあり、Azure portal を使用して初めてその場所にピアリングを設定する場合は、示されているように、既存のピアリング接続が **[ピアリング接続]** セクションに一覧表示されます。 これらのピアリング接続は、Microsoft によって Azure リソースに自動的に変換されるので、それらすべてを新しい接続と共に 1 か所で管理できます。 詳細については、「[ポータルを使用して従来の Direct ピアリングを Azure リソースに変換する](../howto-legacy-direct-portal.md)」を参照してください。
        >

1. **[ピアリング接続]** で **[新規作成]** を選択し、設定する新しい接続ごとに 1 行を追加します。

    * 接続設定を構成または変更するには、行の [編集] ボタンを選択します。

        > [!div class="mx-imgBorder"]
        > ![[編集] ボタン](../media/setup-direct-conf-tab-edit.png)
    
    * 行を削除するには、 **[...]**  >  **[削除]** を選択します。

        > [!div class="mx-imgBorder"]
        > ![[削除] ボタン](../media/setup-direct-conf-tab-delete.png)

    * ここに示すように、接続のすべての設定を指定する必要があります。

         > [!div class="mx-imgBorder"]
         > ![[Direct Peering Connection]\(Direct ピアリング接続\) ページ](../media/setup-direct-conf-tab-connection.png)

        1. 接続を設定する必要がある **[Peering facility]\(ピアリング ファシリティ\)** を選択します。
        1. **[Session Address Provider]\(セッション アドレス プロバイダー\)** は、ネットワークと Microsoft との間の BGP セッションを設定するために必要な、サブネットの提供元を指定するために使用されます。 サブネットを指定できる場合は、 **[ピア]** を選択します。 それ以外の場合は **[Microsoft]** を選択し、[Microsoft ピアリング](mailto:peering@microsoft.com)からの連絡を受けます。 このオプションを選択すると、Microsoft によるピアリング要求の処理に時間がかかります。 場合によっては、Microsoft がサブネットを用意できず、結果として要求が拒否される可能性があります。
        1. **[ピア]** として **[Session Address Provider]\(セッション アドレス プロバイダー\)** オプションを選択した場合は、 **[Session IPv4 prefix]\(セッション IPv4 プレフィックス\)** と **[Session IPv6 prefix]\(セッション IPv6 プレフィックス\)** のボックスそれぞれに、IPv4 アドレスと IPv6 アドレスをプレフィックス マスクと共に入力します。
        1. **[Maximum advertised IPv4 addresses]\(アドバタイズされる最大の IPv4 アドレス\)** と **[Maximum advertised IPv6 addresses]\(アドバタイズされる最大の IPv6 アドレス\)** のボックスそれぞれに、アドバタイズする IPv4 と IPv6 のプレフィックスの番号を入力します。
        1. **[帯域幅の合計]** スライダーを調整して、接続の帯域幅を反映させます。
        1. **[保存]** を選択して接続設定を保存します。

1. 前に選択した **[Metro]\(都市圏\)** 内で、Microsoft がユーザーのネットワークと同一の場所に設置されている任意のファシリティにその他の接続を追加するには、前の手順を繰り返します。

1. 必要なすべての接続を追加したら、 **[確認と作成]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![ピアリングの [構成] タブの最後](../media/setup-direct-conf-tab-final.png)

1. 入力した情報の基本的な検証がポータルで実行されていることがわかります。 上部のリボンには、"*最終検証を実行しています...* " というメッセージが表示されます。

    > [!div class="mx-imgBorder"]
    > ![[ピアリングの検証] タブ](../media/setup-direct-review-tab-validation.png)

1. メッセージが "*検証に成功しました*" に変わったら、情報を確認します。 **[作成]** を選択して、要求を送信します。 要求を変更するには、 **[前へ]** を選択して、手順を繰り返します。

    > [!div class="mx-imgBorder"]
    > ![ピアリングの送信](../media/setup-direct-review-tab-submit.png)

1. 要求を送信したら、デプロイが完了するまで待ちます。 デプロイが失敗した場合は、[Microsoft ピアリング](mailto:peering@microsoft.com)にお問い合わせください。 次に示すように、正常なデプロイが表示されます。

    > [!div class="mx-imgBorder"]
    > ![ピアリング成功](../media/setup-direct-success.png)

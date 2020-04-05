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
ms.openlocfilehash: 2a42ba8809e6895c9eea9f8762513b7fcaa9eb3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75773966"
---
1. **[ピアリングの作成]** ページの **[構成]** タブで、次のようにフィールドに入力します。

    * **[ピアリングの種類]** には、 *[Direct]* を選択します。
    * **[Microsoft ネットワーク]** では *[AS8075]* を選択します。 ASN 8069 を使用してピアリングを作成しないでください。 これは特定のアプリケーション用に予約されており、[Microsoft ピアリング](mailto:peering@microsoft.com)によってのみ使用されます。
    * **[SKU]** には、 *[Basic Free]\(基本無料\)* を選択します。 *[Premium Free]\(プレミアム無料\)* は、特定のアプリケーション用に予約されているため、選択しないでください。
    * ピアリングを設定する**都市圏**の場所を選択します。

        > [!NOTE]
        > 選択した**都市圏**の場所に Microsoft とのピアリング接続が既にあり、初めてポータルを使用してその場所にピアリングを設定する場合は、次に示すように、既存のピアリング接続が **[ピアリング接続]** セクションに一覧表示されます。 Microsoft によって、これらのピアリング接続は Azure リソースに自動的に変換されるので、新しい接続と共にすべてを 1 か所で管理できます。 詳細については、「[ポータルを使用して従来の Direct ピアリングを Azure リソースに変換する](../howto-legacy-direct-portal.md)」を参照してください。
        >

1. **[Peering connections]\(ピアリング接続\)** で **[新規作成]** をクリックして、設定する新しい接続ごとに 1 行を追加します。

    * 接続設定を構成または変更するには、行の [編集] ボタンをクリックします。

        > [!div class="mx-imgBorder"]
        > ![ピアリングの構成 - Direct の編集](../media/setup-direct-conf-tab-edit.png)
    
    * 行を削除するには、 **[...]** ボタン > **[削除]** の順にクリックします。

        > [!div class="mx-imgBorder"]
        > ![ピアリングの構成 - Direct の編集](../media/setup-direct-conf-tab-delete.png)

    * 次に示すように、接続のすべての設定を指定する必要があります。

         > [!div class="mx-imgBorder"]
         > ![ピアリングの構成 - 直接接続](../media/setup-direct-conf-tab-connection.png)

        1. 接続を設定する必要がある **[Peering facility]\(ピアリング ファシリティ\)** を選択します。
        1. **[Session Address Provider]\(セッション アドレス プロバイダー\)** は、ネットワークと Microsoft 間の BGP セッションを設定するために必要なサブネットの提供元を指定するために使用されます。 サブネットを指定できる場合は、 *[ピア]* を選択します。 それ以外の場合は **[Microsoft]** を選択し、[Microsoft ピアリング](mailto:peering@microsoft.com)からの連絡を受けます。 これを選択すると、Microsoft がピアリング要求を処理するための時間がかかることに注意してください。 場合によっては、Microsoft がサブネットを用意できず、結果として要求が拒否される可能性があります。
        1. **[ピア]** として *[Session Address Provider]\(セッション アドレス プロバイダー\)* を選択した場合は、フィールド **[Session IPv4 Prefix]\(セッション IPv4 プレフィックス\)** と **[Session IPv6 Prefix]\(セッション IPv6 プレフィックス\)** それぞれに IPv4 アドレスと IPv6 アドレスをプレフィックス マスクと共に入力します。
        1. **[Maximum advertised IPv4 addresses]\(アドバタイズされる最大の IPv4 アドレス\)** と **[Maximum advertised IPv6 addresses]\(アドバタイズされる最大の IPv6 アドレス\)** のフィールドに、アドバタイズする IPv4 と IPv6 のプレフィックスの番号をそれぞれ入力します。
        1. **[帯域幅の合計]** スライダーを調整して、接続の帯域幅を反映させます。
        1. **[OK]** をクリックして接続設定を保存します。

1. 前に選択した **[Metro]\(都市圏\)** 内で、Microsoft がユーザーのネットワークと同一の場所に設置されている任意のファシリティに接続を追加するには、前の手順を繰り返します。

1. 必要なすべての接続を追加したら、 **[確認と作成]** をクリックします。

    > [!div class="mx-imgBorder"]
    > ![ピアリングの [構成] タブの最後](../media/setup-direct-conf-tab-final.png)

1. 入力した情報の基本的な検証がポータルで実行されていることを確認します。 上部のリボンに、"*最終検証を実行しています...* " と表示されます。

    > [!div class="mx-imgBorder"]
    > ![[ピアリングの検証] タブ](../media/setup-direct-review-tab-validation.png)

1. "*検証に成功しました*" に変わったら、情報を確認し、 **[作成]** をクリックして要求を送信します。 要求を変更する必要がある場合は、 **[前へ]** をクリックし、上記の手順を繰り返してください。

    > [!div class="mx-imgBorder"]
    > ![ピアリングの送信](../media/setup-direct-review-tab-submit.png)

1. 要求を送信したら、デプロイが完了するまで待ちます。 デプロイが失敗した場合は、[Microsoft ピアリング](mailto:peering@microsoft.com)にお問い合わせください。 デプロイが正常に行われると、次のように表示されます。

    > [!div class="mx-imgBorder"]
    > ![ピアリング成功](../media/setup-direct-success.png)

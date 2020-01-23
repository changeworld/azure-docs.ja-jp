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
ms.openlocfilehash: cd51eca0ea4563e1b56f74677df0829669d9e177
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75773726"
---
1. **[ピアリングの作成]** ページの **[構成]** タブで、次のようにフィールドに入力します。

    > [!div class="mx-imgBorder"]
    > ![ピアリングの構成 - Exchange](../media/setup-exchange-conf-tab.png)

    * **[ピアリングの種類]** には、 *[Exchange]* を選択します。
    * **[SKU]** には、 *[Basic Free]\(Basic 無料\)* を選択します。
    * ピアリングを設定する**都市圏**の場所を選択します。

        > [!NOTE]
        > 選択した**都市圏**の場所に Microsoft とのピアリング接続が既にあり、初めてポータルを使用してその場所にピアリングを設定する場合は、次に示すように、既存のピアリング接続が **[ピアリング接続]** セクションに一覧表示されます。 Microsoft は、これらのピアリング接続を自動的に Azure リソースに変換します。これにより、新しい接続と共にそれらをすべてを 1 か所で管理することができます。詳細については、「[ポータルを使用して従来の Exchange ピアリングを Azure リソースに変換する](../howto-legacy-exchange-portal.md)」を参照してください。
        >

1. **[Peering connections]\(ピアリング接続\)** で **[新規作成]** をクリックして、設定する新しい接続ごとに 1 行を追加します。

    * 接続設定を構成または変更するには、行の [編集] ボタンをクリックします。

        > [!div class="mx-imgBorder"]
        > ![ピアリング構成 - Exchange 編集](../media/setup-exchange-conf-tab-edit.png)

    * 行を削除するには、 **[...]** ボタン > **[削除]** の順にクリックします。

        > [!div class="mx-imgBorder"]
        > ![ピアリング構成 - Exchange 編集](../media/setup-exchange-conf-tab-delete.png)

    * 次に示すように、接続のすべての設定を指定する必要があります。

         > [!div class="mx-imgBorder"]
         > ![ピアリング構成 - Exchange 接続](../media/setup-exchange-conf-tab-connection.png)

        1. 接続を設定する必要がある **[Peering facility]\(ピアリング ファシリティ\)** を選択します。
        1. **[IPv4 アドレス]** と **[IPv6 アドレス]** のフィールドに、neighbor コマンドを使用して Microsoft ルーターに構成される IPv4 と IPv6 のアドレスをそれぞれ入力します。
        1. **[Maximum advertised IPv4 addresses]\(アドバタイズされる最大の IPv4 アドレス\)** と **[Maximum advertised IPv6 addresses]\(アドバタイズされる最大の IPv6 アドレス\)** のフィールドに、アドバタイズする IPv4 と IPv6 のプレフィックスの番号をそれぞれ入力します。
        1. **[OK]** をクリックして接続設定を保存します。

1. 前に選択した**都市圏**内で、Microsoft がユーザーのネットワークと同一の場所に設置されている任意のファシリティに接続を追加するには、前の手順を繰り返してください。

1. 必要なすべての接続を追加したら、 **[確認と作成]** をクリックします。

    > [!div class="mx-imgBorder"]
    > ![ピアリングの [構成] タブの最後](../media/setup-exchange-conf-tab-final.png)

1. 入力した情報の基本的な検証がポータルで実行されていることを確認します。 上部のリボンに、"*最終検証を実行しています...* " と表示されます。

    > [!div class="mx-imgBorder"]
    > ![[ピアリングの検証] タブ](../media/setup-direct-review-tab-validation.png)

1. "*検証に成功しました*" に変わったら、情報を確認し、 **[作成]** をクリックして要求を送信します。 要求を変更する必要がある場合は、 **[前へ]** をクリックし、上記の手順を繰り返してください。

    > [!div class="mx-imgBorder"]
    > ![ピアリングの送信](../media/setup-exchange-review-tab-submit.png)

1. 要求を送信したら、デプロイが完了するまで待ちます。 デプロイが失敗した場合は、[Microsoft ピアリング](mailto:peering@microsoft.com)にお問い合わせください。 デプロイが正常に行われると、次のように表示されます。

    > [!div class="mx-imgBorder"]
    > ![ピアリング成功](../media/setup-direct-success.png)

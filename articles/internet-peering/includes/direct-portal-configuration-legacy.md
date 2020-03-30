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
ms.openlocfilehash: 16909e0a5d59b0ae019d91aad00e0168b0c5f433
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75773902"
---
1. **[ピアリングの作成]** ページの **[構成]** タブで、次のようにフィールドに入力します。

    > [!div class="mx-imgBorder"]
    > ![ピアリングの構成 - Direct](../media/setup-direct-conf-tab.png)

    * **[ピアリングの種類]** には、 *[Direct]* を選択します。
    * **[Microsoft ネットワーク]** では *[AS8075]* を選択します。 [ASN 8069] は選択しないでください。 これは特定のアプリケーション用に予約されており、[Microsoft ピアリング](mailto:peering@microsoft.com)によってのみ使用されます。
    * **[SKU]** には、 *[Basic Free]\(基本無料\)* を選択します。 *[Premium Free]\(プレミアム無料\)* は、特定のアプリケーション用に予約されているため、選択しないでください。
    * ピアリングを Azure リソースに変換する**都市圏**の場所を選択します。 選択した**都市圏**の場所に、Azure リソースに変換されない Microsoft とのピアリング接続がある場合、そのような接続は、以下に示すように **[ピアリング接続]** セクションに一覧表示されます。 次に、それらのピアリング接続を Azure リソースに変換することができます。

        > [!div class="mx-imgBorder"]
        > ![ピアリング接続 - Direct - レガシ接続](../media/setup-directlegacy-conf-tab.png)

1. 帯域幅を更新する必要がある場合は、以下で強調表示されているように、回線の [編集] ボタンをクリックして、接続の設定を変更します。

    > [!div class="mx-imgBorder"]
    > ![ピアリングの構成 - Direct の編集](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > 選択した**都市圏**の場所に、Microsoft とのピアリング接続を追加したい場合は、 **[新規作成]** ボタンをクリックして実行できます。 詳細については、「[ポータルを使用して Direct ピアリングを作成または変更する](../howto-direct-portal.md)」を参照してください。
    >

1. **[確認と作成]** をクリックします。 入力した情報の基本的な検証がポータルで実行されていることを確認します。 上部のリボンに、"*最終検証を実行しています...* " と表示されます。

    > [!div class="mx-imgBorder"]
    > ![[ピアリングの検証] タブ](../media/setup-direct-review-tab-validation.png)

1. "*検証に成功しました*" に変わったら、情報を確認し、 **[作成]** をクリックして要求を送信します。 要求を変更する必要がある場合は、 **[前へ]** をクリックし、上記の手順を繰り返してください。

    > [!div class="mx-imgBorder"]
    > ![ピアリングの送信](../media/setup-direct-review-tab-submit.png)

1. 要求を送信したら、デプロイが完了するまで待ちます。 デプロイが失敗した場合は、[Microsoft ピアリング](mailto:peering@microsoft.com)にお問い合わせください。 デプロイが正常に行われると、次のように表示されます。

    > [!div class="mx-imgBorder"]
    > ![ピアリング成功](../media/setup-direct-success.png)

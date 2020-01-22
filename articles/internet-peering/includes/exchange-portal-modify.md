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
ms.openlocfilehash: e3e87e59f5b3c95051d9ee53e4b8d87afe9d9ba8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75773746"
---
このセクションでは、Direct ピアリングで次の変更操作を実行する方法について説明します。

### <a name="add-exchange-peering-connections"></a>Exchange ピアリング接続を追加する

1. 上部にある **[+ Add connections]\ (+ 接続の追加\)** ボタンをクリックし、新しいピアリング接続を構成します。
    > [!div class="mx-imgBorder"]
    > ![ピアリング リソース ビュー](../media/setup-exchange-modify-addconnection.png)
1. **[Exchange peering Connection]\(Exchange ピアリング接続\)** フォームに入力して、 **[保存]** をクリックします。 ピアリング接続の構成の詳細については、上記の Direct ピアリングを作成およびプロビジョニングする方法に関するセクションで手順を確認してください。
    > [!div class="mx-imgBorder"]
    > ![ピアリング リソース ビュー](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Exchange ピアリング接続を削除する

1. 削除するピアリング接続をクリックし、 **[...]**  >  **[接続の削除]** ボタンをクリックします。
    > [!div class="mx-imgBorder"]
    > ![ピアリング接続の削除](../media/setup-exchange-modify-deleteconnection.png)
1. 強調表示されているボックスに示されているように、 **[削除の確認]** ボックスにリソース ID を入力し、 **[削除]** をクリックします。
    > [!div class="mx-imgBorder"]
    > ![ピアリング接続の削除の確認](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>アクティブな接続の IPv4/IPv6 セッションを追加する

1. 変更するピアリング接続をクリックし、 **[...]**  >  **[接続の編集]** ボタンをクリックします。
    > [!div class="mx-imgBorder"]
    > ![ピアリング接続の編集](../media/setup-exchange-modify-editconnection.png)
1. **IPv4 アドレス**または **IPv6 アドレス**の情報を追加し、 **[保存]** をクリックします。
    > [!div class="mx-imgBorder"]
    > ![ピアリング接続の変更](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>アクティブな接続の IPv4/IPv6 セッションを削除する

ポータルでは、既存の接続から IPv4/IPv6 セッションを削除することは現在サポートされていません。 [Microsoft ピアリング](mailto:peeringexperience@microsoft.com)にお問い合わせください。
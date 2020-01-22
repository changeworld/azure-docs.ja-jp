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
ms.openlocfilehash: d9ff01cf0180dae7f75d9753ba889d0caddad937
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75773986"
---
このセクションでは、Direct ピアリングで次の変更操作を実行する方法について説明します。

### <a name="add-direct-peering-connections"></a>Direct ピアリング接続を追加する
1. 上部にある **[+ Add connections]\(+ 接続の追加\)** ボタンをクリックし、新しいピアリング接続を構成します。
    > [!div class="mx-imgBorder"]
    > ![ピアリング リソース ビュー](../media/setup-direct-modify-addconnection.png)
1. **[Direct peering Connection]\(Direct ピアリング接続\)** フォームに入力して、 **[保存]** をクリックします。 ピアリング接続の構成の詳細については、上記の Direct ピアリングを作成およびプロビジョニングする方法に関するセクションで手順を確認してください。
    > [!div class="mx-imgBorder"]
    > ![ピアリング リソース ビュー](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Direct ピアリング接続を削除する

接続の削除は、現在ポータルではサポートされていません。 [Microsoft ピアリング](mailto:peeringexperience@microsoft.com)にお問い合わせください。

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>アクティブな接続で帯域幅をアップグレードまたはダウングレードする
1. 変更するピアリング接続をクリックし、 **[...]**  >  **[接続の編集]** ボタンをクリックします。
    > [!div class="mx-imgBorder"]
    > ![ピアリング接続の編集](../media/setup-direct-modify-editconnection.png)
1. 次に示すように帯域幅を変更し、 **[保存]** をクリックします。
    > [!div class="mx-imgBorder"]
    > ![ピアリング接続の帯域幅の変更](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>アクティブな接続で IPv4/IPv6 セッションを追加します。
1. 変更するピアリング接続をクリックし、上記のように **[...]**  >  **[接続の編集]** ボタンをクリックします。
1. **セッション IPv4 プレフィックス**または**セッション IPv6 プレフィックス**の情報を追加し、 **[保存]** をクリックします。

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>アクティブな接続で IPv4/IPv6 セッションを削除します。
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).

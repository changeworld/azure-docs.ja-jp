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
ms.openlocfilehash: e06b5261ca6923e158c818d236a30cf6ebff189b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680913"
---
このセクションでは、Direct ピアリングで次の変更操作を実行する方法について説明します。

### <a name="add-exchange-peering-connections"></a>Exchange ピアリング接続を追加する

1. **[+ Add connections]\(+ 接続の追加\)** ボタンを選択し、新しいピアリング接続を構成します。
    > [!div class="mx-imgBorder"]
    > ![ピアリング リソース ビュー](../media/setup-exchange-modify-addconnection.png)
1. **[Exchange peering Connection]\(Exchange ピアリング接続\)** フォームに入力して、 **[保存]** を選択します。 ピアリング接続の構成のヘルプについては、「Direct ピアリングを作成およびプロビジョニングする」セクションの手順を確認してください。
    > [!div class="mx-imgBorder"]
    > ![Exchange ピアリング接続フォーム](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Exchange ピアリング接続を削除する

1. 削除するピアリング接続を選択して、 **[...]**  >  **[接続の削除]** を選択します。
    > [!div class="mx-imgBorder"]
    > ![[接続の削除] ボタン](../media/setup-exchange-modify-deleteconnection.png)
1. **[削除の確認]** ボックスにリソース ID を入力し、 **[削除]** を選択します。
    > [!div class="mx-imgBorder"]
    > ![削除の確認](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>アクティブな接続で IPv4 または IPv6 セッションを追加する

1. 変更するピアリング接続を選択して、 **[...]**  >  **[接続の編集]** を選択します。
    > [!div class="mx-imgBorder"]
    > ![[接続の編集] ボタン](../media/setup-exchange-modify-editconnection.png)
1. **IPv4 アドレス**または **IPv6 アドレス**の情報を追加し、 **[保存]** を選択します。
    > [!div class="mx-imgBorder"]
    > ![ピアリング接続の変更](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>アクティブな接続の IPv4 または IPv6 セッションを削除する

既存の接続から IPv4 または IPv6 セッションを削除することは、現在ポータルではサポートされていません。 詳細については、[Microsoft ピアリング](mailto:peeringexperience@microsoft.com)にお問い合わせください。
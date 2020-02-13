---
title: Azure VMware Solutions (AVS) - パブリック IP アドレスの割り当て
description: AVS プライベート クラウド環境で仮想マシンにパブリック IP アドレスを割り当てる方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 87133f5efb9f096d3fdb0956aab1caac58b4bd94
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024298"
---
# <a name="allocate-public-ip-addresses-for-avs-private-cloud-environment"></a>AVS プライベート クラウド環境にパブリック IP アドレスを割り当てる

[ネットワーク] ページで [パブリック IP] タブを開き、AVS プライベート クラウド環境内の仮想マシンにパブリック IP アドレスを割り当てます。

1. [AVS ポータルにアクセスし](access-cloudsimple-portal.md)、サイド メニューの **[ネットワーク]** を選択します。
2. **[パブリック IP]** を選択します。
3. **[新しいパブリック IP]** をクリックします。

    ![[パブリック IP] ページ](media/public-ips-page.png)

4. IP アドレス エントリを識別する名前を入力します。
5. 既定の場所はそのままにします。
6. 必要であれば、スライダーを使用してアイドル タイムアウトを変更します。
7. パブリック IP アドレスを割り当てる対象となるローカル IP アドレスを入力します。
8. 関連付けられた DNS 名を入力します。
9. **[送信]** をクリックします。

![パブリック IP を割り当てる](media/network-public-ip-allocate.png)

パブリック IP アドレスの割り当てのタスクが開始します。 タスクの状態を **[Activity]\(アクティビティ\) > [Tasks]\(タスク\)** ページで確認できます。 割り当てが完了したら、新しいエントリがパブリック IP ページに表示されます。

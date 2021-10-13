---
title: Azure VMware Solution by CloudSimple - パブリック IP アドレスの割り当て
description: プライベート クラウド環境で仮想マシンのパブリック IP アドレスを割り当てる方法について説明します。
author: suzizuber
ms.author: v-szuber
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 121baac8c8a5e881c63f16376f947fce315bd238
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616153"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>プライベート クラウド環境のためにパブリック IP アドレスを割り当てる

[ネットワーク] ページで [パブリック IP] タブを開き、プライベート クラウド環境の仮想マシンにパブリック IP アドレスを割り当てます。

1. [CloudSimple ポータルにアクセスし](access-cloudsimple-portal.md)、サイド メニューの **[ネットワーク]** を選択します。
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

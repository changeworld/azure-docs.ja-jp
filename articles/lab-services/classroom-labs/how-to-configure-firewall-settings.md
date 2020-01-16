---
title: Azure Lab Services のファイアウォール設定
description: 情報をファイアウォール規則に追加できるように、ラボ内の仮想マシンのパブリック IP アドレスとポート番号の範囲を特定する方法について説明します。
author: emaher
ms.author: enewman
ms.date: 12/12/2019
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: da1614e4a3e02ed91ef2d3c59ac4eb3eac0dcc7c
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692639"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Azure Lab Services のファイアウォール設定

組織や学校はそれぞれ、自分たちのニーズに最も合った方法で、所有するネットワークを設定します。  時にはそれに、所有するネットワークの外にあるマシンに対するリモート デスクトップ プロトコル (rdp) 接続や Secure Shell (ssh) 接続をブロックするファイアウォール規則の設定が含まれています。  Azure Lab Services はパブリック クラウドで実行されるため、キャンパス ネットワークからの接続時に VM へのアクセスを学生に許可するために、追加の構成が必要になる場合があります。

各ラボで、単一のパブリック IP アドレスと複数のポートを使用します。  すべての VM、つまりテンプレート VM と 学生 VM の両方で、このパブリック IP アドレスを使用します。  ラボが使われている間、パブリック IP アドレスは変更されません。  ただし、各 VM のポート番号は異なるものになります。  ポート番号の範囲は 49152 ～ 65535 です。  指導者と学生を正しい VM に接続するために、パブリック IP アドレスとポート番号の組み合わせが使用されます。  この記事では、ラボで使用される特定のパブリック IP アドレスを見つける方法について説明します。  この情報は、学生が自分の VM にアクセスできるように、インバウンドとアウトバウンドのファイアウォール規則を更新するために使用できます。

>[!IMPORTANT]
>各ラボは、異なるパブリック IP アドレスを持つことになります。

## <a name="find-public-ip-for-a-lab"></a>ラボのパブリック IP を見つける

各ラボのパブリック IP アドレスは、Lab Services のラボ アカウントの **[すべてのラボ]** ブレードに一覧表示されます。  **[すべてのラボ]** ブレードを見つける方法については、[ラボ アカウントでラボを管理する方法](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account)に関するページを参照してください。  

> [!div class="mx-imgBorder"]
> ![[すべてのラボ] ブレード](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>ラボのテンプレート マシンがまだ公開されていない場合、パブリック IP アドレスは表示されません。

## <a name="conclusion"></a>まとめ

これでラボのパブリック IP アドレスがわかりました。  パブリック IP アドレスとポート範囲 49152 ～ 65535 について、組織のファイアウォールのインバウンド規則とアウトバウンド規則を作成できます。  ルールが更新されると、学生はネットワーク ファイアウォールでアクセスがブロックされることなく、自分の VM にアクセスできます。

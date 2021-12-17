---
title: 'Azure Bastion:仮想マシン セッションの表示: 全画面表示'
description: RDP または SSH 接続の Azure Bastion で、仮想マシンのビューをお使いのブラウザーで全画面表示に変更して戻す方法について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 08/30/2021
ms.author: cherylmc
ms.openlocfilehash: 82c763647911037985dc3395a2176e8c43f34114
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128629580"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion"></a>VM セッションを全画面表示に変更する:Azure Bastion

この記事は、ブラウザーの仮想マシンのビューを全画面表示にしたり元に戻したりする際に役立ちます。 VM を操作する前に、[Azure Bastion ホストを作成する](./tutorial-create-host-portal.md)手順を実行済みであることを確認してください。 その後、[RDP](bastion-connect-vm-rdp-windows.md) または [SSH](bastion-connect-vm-ssh-linux.md) のいずれかを使用して、操作する VM に接続します。

## <a name="launch-the-clipboard-tool"></a>クリップボード ツールを起動する

リモート セッションの間に、画面の左中央にある 2 つの矢印を選択して Azure Bastion クリップボード アクセス ツール パレットを起動します。

![ツール](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>全画面表示を選択する

**[全画面表示]** ボタンを選択して、セッションを全画面エクスペリエンスに切り替えます。 切り替えると、セッションが全画面表示に再初期化されます。

![全画面表示](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>次のステップ

[Azure Bastion に関する FAQ](bastion-faq.md) を読む。
Azure VM との間で[コピーと貼り付け](bastion-vm-copy-paste.md)を行う方法を確認する。

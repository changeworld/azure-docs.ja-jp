---
title: Azure Bastion で仮想マシン セッションのビューを全画面表示に変更する | Microsoft Docs
description: この記事では、ビューを全画面表示に変更する方法について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: a8c9cab825644fe797713c80d3a710c7a2d54850
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498054"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion"></a>VM セッションを全画面表示に変更する:Azure Bastion

この記事は、ブラウザーの仮想マシンのビューを全画面表示にしたり元に戻したりする際に役立ちます。 VM を操作する前に、[Azure Bastion ホストを作成する](bastion-create-host-portal.md)手順を実行済みであることを確認してください。 その後、[RDP](bastion-connect-vm-rdp.md) または [SSH](bastion-connect-vm-ssh.md) のいずれかを使用して、操作する VM に接続します。

## <a name="launch-the-clipboard-tool"></a>クリップボード ツールを起動する

リモート セッションの間に、画面の左中央にある 2 つの矢印を選択して Azure Bastion クリップボード アクセス ツール パレットを起動します。

![ツール](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>全画面表示を選択する

**[全画面表示]** ボタンを選択して、セッションを全画面エクスペリエンスに切り替えます。 切り替えると、セッションが全画面表示に再初期化されます。

![全画面表示](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>次の手順

[Azure Bastion に関する FAQ](bastion-faq.md) を読む。
Azure VM との間で[コピーと貼り付け](bastion-vm-copy-paste.md)を行う方法を確認する。
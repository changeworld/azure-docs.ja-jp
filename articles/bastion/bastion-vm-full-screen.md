---
title: 'Azure Bastion:仮想マシン セッションの表示: 全画面表示'
description: この記事では、ビューを全画面表示に変更する方法について説明します。
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: f4aa2c5aa3f40802db7be64eeb778866819bfe67
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84744223"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion"></a>VM セッションを全画面表示に変更する:Azure Bastion

この記事は、ブラウザーの仮想マシンのビューを全画面表示にしたり元に戻したりする際に役立ちます。 VM を操作する前に、[Azure Bastion ホストを作成する](bastion-create-host-portal.md)手順を実行済みであることを確認してください。 その後、[RDP](bastion-connect-vm-rdp.md) または [SSH](bastion-connect-vm-ssh.md) のいずれかを使用して、操作する VM に接続します。

## <a name="launch-the-clipboard-tool"></a>クリップボード ツールを起動する

リモート セッションの間に、画面の左中央にある 2 つの矢印を選択して Azure Bastion クリップボード アクセス ツール パレットを起動します。

![ツール](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>全画面表示を選択する

**[全画面表示]** ボタンを選択して、セッションを全画面エクスペリエンスに切り替えます。 切り替えると、セッションが全画面表示に再初期化されます。

![全画面表示](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>次のステップ

[Azure Bastion に関する FAQ](bastion-faq.md) を読む。
Azure VM との間で[コピーと貼り付け](bastion-vm-copy-paste.md)を行う方法を確認する。

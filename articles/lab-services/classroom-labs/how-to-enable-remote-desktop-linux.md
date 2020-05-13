---
title: Azure Lab Services の Linux 向けリモート デスクトップを有効にする | Microsoft Docs
description: Azure Lab Services のラボで Linux 仮想マシン向けリモート デスクトップを有効にする方法について説明します。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2020
ms.author: spelluru
ms.openlocfilehash: dfd16d0977504e598492225238d29c83fcb7a028
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598064"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Azure Lab Services のラボの Linux 仮想マシンでリモート デスクトップを有効にする
この記事では、次のタスクの手順について説明します。

- Linux VM のリモート デスクトップを有効にする
- 教師がリモート デスクトップ接続 (RDP) 経由でテンプレート VM に接続する

## <a name="enable-remote-desktop-for-linux-vm"></a>Linux VM のリモート デスクトップを有効にする
教師はラボの作成時に **Linux** イメージの**リモート デスクトップ接続**を有効にすることができます。 テンプレートに対して Linux イメージが選択されていると、 **[Enable Remote Desktop Connection]\(リモート デスクトップ接続を有効にする\)** オプションが表示されます。 このオプションが有効になっていると、教師が RDP (リモート デスクトップ) を介してテンプレート VM と学生用 VM に接続することができます。 

![Linux イメージのリモート デスクトップ接続を有効にする](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

**[Enabling Remote Desktop Connection]\(リモート デスクトップ接続の有効化\)** メッセージ ボックスで、 **[Continue with Remote Desktop]\(リモート デスクトップで続行\)** を選択します。 

![Linux イメージのリモート デスクトップ接続を有効にする](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> **リモート デスクトップ接続**を有効にすると、Linux マシンの **RDP** ポートのみが開きます。 仮想マシン イメージに RDP が既にインストールされて構成されている場合、追加の手順に従わなくても RDP 経由で VM に接続できます。
> 
> VM イメージに RDP がインストールおよび構成されていない場合、最初に SSH を使用して Linux マシンに接続し、RDP と GUI のパッケージをインストールします。これで、後で RDP を使用して Linux マシンに接続できるようになります。 詳しくは、「[リモート デスクトップをインストールして Azure の Linux VM に接続するように構成する](../../virtual-machines/linux/use-remote-desktop.md)」を参照してください。 次に、学生が学生の Linux VM にリモート デスクトップ接続できるように、イメージを発行します。 

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム
現時点では、次のオペレーティング システムのリモート デスクトップ接続がサポートされています。

- openSUSE Leap 42.3
- CentOS-based 7.5
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="connect-to-the-template-vm"></a>テンプレート VM に接続する 
教師は最初に SSH を使用してテンプレート VM に接続し、これに RDP と GUI のパッケージをインストールする必要があります。 その後、教師は RDP を使用し、テンプレート VM に接続できます。 

1. ツール バーに **[Customize template]\(テンプレートのカスタマイズ\)** が表示されていれば、それを選択します。 次に、 **[Customize template]\(テンプレートのカスタマイズ\)** ダイアログ ボックスで **[続行]** を選択します。 この操作によりテンプレート VM が開始されます。  

    ![テンプレートのカスタマイズ](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. テンプレート VM が起動したら、ツール バーで **[Connect template]\(接続テンプレート\)** を選択し、それから **[Connect via SSH]\(SSH 経由で接続する\)** を選択できます。 

    ![ラボの作成後に RDP 経由でテンプレートに接続する](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. **[仮想マシンに接続する]** ダイアログ ボックスが表示されます。 テキスト ボックスの横にある **[コピー]** ボタンを選択して、クリップボードにコピーします。 SSH 接続文字列を保存します。 仮想マシンに接続するには、SSH ターミナル ([Putty](https://www.putty.org/) など) からこの接続文字列を使用します。
 
    ![SSH 接続文字列](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. 自分と生徒が後で RDP を利用して Linux コンピューターに接続できるように RDP パッケージと GUI パッケージをインストールします。 詳しくは、「[リモート デスクトップをインストールして Azure の Linux VM に接続するように構成する](../../virtual-machines/linux/use-remote-desktop.md)」を参照してください。 次に、学生が学生の Linux VM にリモート デスクトップ接続できるように、イメージを発行します。
5. これらのパッケージをインストールすると、ツール バーの **[Connect to template]\(テンプレートに接続\)** を使用し、それから **[Connect via RDP]\(RDP 経由で接続する\)** を選択し、RDP 経由でテンプレート VM に接続できます。 RDP ファイルを保存し、それを使用して RDP 経由でテンプレート VM に接続します。 

## <a name="next-steps"></a>次のステップ
リモート デスクトップ接続機能をインストラクターが有効にした後で、学生が RDP と SSH 経由でその VM に接続することはできません。 詳細については、[クラスルーム ラボの Linux VM でリモート デスクトップを使用する方法](how-to-use-remote-desktop-linux-student.md)に関するページを参照してください。 
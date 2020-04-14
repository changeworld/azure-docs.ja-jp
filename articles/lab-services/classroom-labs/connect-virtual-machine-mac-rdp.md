---
title: Mac から Azure Lab Services VM に接続する方法 | Microsoft Docs
description: この記事の内容は次のとおりです。
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2020
ms.author: spelluru
ms.openlocfilehash: 0d3484c1008e00bcfde3adb399e925c0e054f49e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79504111"
---
# <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>MAC から RDP を使用して VM に接続する
このセクションでは、Mac から RDP を使用してクラスルーム ラボ VM に接続する方法を学生向けに説明します。

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Microsoft リモート デスクトップを MAC にインストールする
1. MAC で App Store を開き、**Microsoft リモート デスクトップ**を検索します。

    ![Microsoft リモート デスクトップ](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. 最新バージョンの Microsoft リモート デスクトップをインストールします。 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>MAC から RDP を使用して VM にアクセスする
1. **Microsoft リモート デスクトップ**がインストールされているコンピューターで、ダウンロードした **RDP** ファイルを開きます。 VM への接続が開始されます。 

    ![VM への接続](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. 次の警告が表示された場合は、 **[Continue]\(続行\)** を選択します。 

    ![証明書の警告](../media/how-to-use-classroom-lab/certificate-error.png)
1. VM が表示されます。 

    > [!NOTE]
    > 次に示したのは、CentOS Linux VM の例です。 

    ![VM](../media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>次のステップ
RDP を使用して Linux VM に接続する方法については、[Linux 仮想マシン用リモート デスクトップの使用](how-to-use-remote-desktop-linux-student.md)に関するページをご覧ください



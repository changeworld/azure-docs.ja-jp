---
title: Chromebook から Azure Lab Services VM に接続する方法 | Microsoft Docs
description: Azure Lab Services で Chromebook から仮想マシンに接続する方法について説明します。
services: devtest-lab, lab-services, virtual-machines
author: nicolela
ms.topic: how-to
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 7fb33b580aa0984366b47f0358df7f915a77730b
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176151"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-chromebook"></a>Chromebook でリモート デスクトップ プロトコル (RDP) を使用して VM に接続する

このセクションでは、Chromebook から RDP を使用してクラスルーム ラボ VM に接続する方法を学生向けに説明します。

## <a name="install-microsoft-remote-desktop-on-a-chromebook"></a>Microsoft リモート デスクトップを Chromebook にインストールする

1. Chromebook で App Store を開き、**Microsoft リモート デスクトップ** を検索します。

    ![Microsoft リモート デスクトップ](./media/how-to-use-classroom-lab/install-ms-remote-desktop-chromebook.png)
    
1. 最新バージョンの Microsoft リモート デスクトップをインストールします。 

## <a name="access-the-vm-from-your-chromebook-using-rdp"></a>Chromebook から RDP を使用して VM にアクセスする

1. **Microsoft リモート デスクトップ** がインストールされているコンピューターで、ダウンロードした **RDP** ファイルを開きます。 VM への接続が開始されます。 

    ![VM への接続](./media/how-to-use-classroom-lab/connect-vm-chromebook.png)

1. パスワードの入力を求めるメッセージが表示されたら、パスワードを入力します。

    ![ユーザー名とパスワードを入力する [ログオン] 画面を示すスクリーンショット。](./media/how-to-use-classroom-lab/password-chromebook.png)

1. 次の警告が表示された場合は、 **[Continue]\(続行\)** を選択します。 

    ![証明書の警告](./media/how-to-use-classroom-lab/certificate-error-chromebook.png)

1. 接続先の VM のデスクトップが表示されます。

## <a name="next-steps"></a>次のステップ

Linux VM への接続の詳細については、[Linux 仮想マシンへの接続](how-to-use-remote-desktop-linux-student.md)に関する記事を参照してください


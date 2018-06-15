---
title: Azure Lab Services でクラスルーム ラボにアクセスする方法 | Microsoft Docs
description: このチュートリアルでは、教師によって設定されたクラスルーム ラボ内の仮想マシンにアクセスします。
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 85da6989740446c980bcb9f2c89a6c31afe488fb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34651560"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Azure Lab Services でクラスルーム ラボにアクセスする方法
この記事では、クラスルーム ラボにアクセスし、ラボの VM に接続して、VM を停止する方法について説明します。 

## <a name="view-all-the-classroom-labs"></a>すべてのクラスルーム ラボを表示する

1. 教師から受け取った**登録 URL** に移動します。 
2. 学校アカウントを使ってサービスにサインインし、登録を完了します。 
3. 登録した後、アクセスできるラボの仮想マシンが表示されることを確認します。 

    ![すべてのラボを表示する](../media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>クラスルーム ラボの仮想マシンに接続する

1. アクセスするラボの仮想マシンを表すタイルの **[接続]** を選びます。

    ![すべてのラボを表示する](../media/how-to-use-classroom-lab/connect-button.png)
2. 仮想マシンの準備ができるまでに数分かかります。

    ![仮想マシンを準備する](../media/how-to-use-classroom-lab/getting-virtual-machine-ready.png)
3. RDP ファイルをハード ディスクに保存して開きます。 
    
    ![RDP ファイルを保存する](../media/how-to-use-classroom-lab/save-rdp-file.png)
4. マシンにログインするために教師から提供された**ユーザー名**と**パスワード**を使います。 

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>クラスルーム ラボの仮想マシンを停止する

クラスルーム ラボを表すタイルで **[停止]** を選びます。 VM が停止すると、タイルの **[開始]** ボタンが有効になります。 

## <a name="next-steps"></a>次の手順
Azure Lab Services を使用してラボの設定を開始します。

- [クラスルーム ラボを設定する](how-to-manage-classroom-labs.md)
- [ラボを設定する](../tutorial-create-custom-lab.md)

 
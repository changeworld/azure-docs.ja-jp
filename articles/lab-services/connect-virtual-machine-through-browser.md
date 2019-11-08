---
title: ブラウザーを使用して仮想マシンに接続する - Azure | Microsoft Docs
description: ブラウザーを使用して仮想マシンに接続する方法について説明します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 1bb5d979ff7c9fe6e2afec4c7ebd81649b4488e3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581227"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>ブラウザーを使用して仮想マシンに接続する 

DevTest Labs は [Azure Bastion](https://docs.microsoft.com/azure/bastion/) と統合されたことにより、ブラウザーを使用して仮想マシンに接続できます。 DevTest Labs でこの機能を有効にする方法の詳細については、「[ラボ仮想マシンでブラウザー接続を有効にする](enable-browser-connection-lab-virtual-machines.md)」を参照してください。

"*ブラウザー接続*" が有効になると、ラボ ユーザーはブラウザーを使用して仮想マシンにアクセスできます。  


## <a name="create-a-lab-virtual-machine"></a>ラボ仮想マシンの作成

まず、Bastion が構成されている VNet 内にラボ仮想マシンを作成する必要があります。 仮想マシンの作成中に **[詳細設定]** タブに移動すると、VNet を選択できます。

![仮想マシンの作成](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>ブラウザーでの仮想マシンの起動

仮想マシンを作成したら、 *[ブラウザー接続]* ボタンをクリックして、マシンのユーザー名とパスワードを入力すると、ブラウザーで起動できます。  

![ブラウザーでの起動](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>次の手順

[Azure DevTest Labs でラボに VM を追加する](devtest-lab-add-vm.md)

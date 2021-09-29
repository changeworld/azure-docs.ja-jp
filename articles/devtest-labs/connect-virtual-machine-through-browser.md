---
title: ブラウザーを使用して仮想マシンに接続する
description: ブラウザーを使用して仮想マシンに接続する方法について説明します。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: dbbaa4286aac91e362a2024f1705add8f48d566d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645001"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>ブラウザーを使用して仮想マシンに接続する 

DevTest Labs は [Azure Bastion](../bastion/index.yml) と統合されたことにより、ブラウザーを使用して仮想マシンに接続できます。 DevTest Labs でこの機能を有効にする方法の詳細については、「[ラボ仮想マシンでブラウザー接続を有効にする](enable-browser-connection-lab-virtual-machines.md)」を参照してください。

"*ブラウザー接続*" が有効になると、ラボ ユーザーはブラウザーを使用して仮想マシンにアクセスできます。  

## <a name="create-a-lab-virtual-machine"></a>ラボ仮想マシンの作成

まず、Bastion が構成されている仮想ネットワーク内にラボ仮想マシンを作成する必要があります。 AzureBastionSubnet ではなく、作成した 2 番目の **サブネット** を選択します。 仮想マシンの作成中に **[詳細設定]** タブに移動することによって仮想ネットワークを選択できます。

![仮想マシンの作成](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>ブラウザーでの仮想マシンの起動

仮想マシンを作成したら、*[ブラウザー接続]* ボタンをクリックして、マシンのユーザー名とパスワードを入力すると、ブラウザーで起動できます。  

![ブラウザーでの起動](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>次の手順

[Azure DevTest Labs でラボに VM を追加する](devtest-lab-add-vm.md)

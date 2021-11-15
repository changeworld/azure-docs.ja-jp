---
title: ラボの仮想マシンへのブラウザー アクセスを有効にする
description: ブラウザーを使用して仮想マシンに接続する方法について説明します。
ms.topic: how-to
ms.date: 10/29/2021
ms.openlocfilehash: f712d0090defa28f673807b835dbe3642cfdface
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464370"
---
# <a name="connect-to-your-lab-virtual-machines-through-a-browser"></a>ブラウザーを使用してラボの仮想マシンに接続する 

DevTest Labs は [Azure Bastion](../bastion/index.yml) と統合されたことにより、ブラウザーを使用してラボの仮想マシン (VM) に接続できます。 **ブラウザー接続** が有効になると、ラボ ユーザーはブラウザーを使用してその仮想マシンにアクセスできます。  

この攻略ガイドでは、**ブラウザー接続** を使用してラボの VM に接続します。

## <a name="prerequisites"></a>前提条件

- ラボの VM で、[仮想ネットワークに Bastion が構成されていること、また **[ブラウザー接続]** 設定が有効にされていること](enable-browser-connection-lab-virtual-machines.md)。

- Web ブラウザーが、`https://portal.azure.com:443` からのポップアップを許可するように構成されていること。

## <a name="launch-virtual-machine-in-a-browser"></a>ブラウザーでの仮想マシンの起動

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **DevTest Labs** のラボに移動します。

1. 仮想マシンを選択します。

1. トップ メニューから **[ブラウザー接続]** を選択します。

1. **[ブラウザー接続]** セクションで自分の資格情報を入力し、 **[接続]** を選択します。

    :::image type="content" source="./media/connect-virtual-machine-through-browser/lab-vm-browser-connect.png" alt-text="ブラウザーの [接続] ボタンのスクリーンショット。":::

## <a name="next-steps"></a>次の手順

[Azure DevTest Labs でラボに VM を追加する](devtest-lab-add-vm.md)

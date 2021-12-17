---
title: Windows 仮想マシンに接続する
description: ラボ (Azure DevTest Labs) で Windows 仮想マシンに接続する方法について説明します
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 8fe7b93ce7aa2cf5521bd53393b086e221e84d2c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588012"
---
# <a name="connect-to-a-windows-vm-in-your-lab-azure-devtest-labs"></a>ラボで Windows VM に接続する (Azure DevTest Labs)
この記事では、ラボで Windows VM に接続する方法について説明します。 

## <a name="connect-to-a-windows-vm"></a>Windows VM に接続する
1. [Azure portal](https://portal.azure.com) にサインインします。
1. 検索バーで "**DevTest Labs**" を検索して選択します。 

    :::image type="content" source="./media/connect-windows-virtual-machine/search-select.png" alt-text="DevTest Labs の検索と選択":::    
1. ラボの一覧で、目的の **ラボ** を選択します。

    :::image type="content" source="./media/connect-windows-virtual-machine/select-lab.png" alt-text="ラボの選択":::            
1. ラボのホーム ページで、 **[仮想マシン]** の一覧から使用している Windows VM を選択します。 

    :::image type="content" source="./media/connect-windows-virtual-machine/select-windows-vm.png" alt-text="Windows VM の選択":::                
1. VM の **[仮想マシン]** ページで、ツール バーの **[接続]** を選択します。 VM が停止している場合は、最初に **[開始]** を選択して VM を起動します。

    :::image type="content" source="./media/connect-windows-virtual-machine/select-connect.png" alt-text="ツール バーの [接続] を選択する":::                    
1. Edge ブラウザーを使用している場合は、ブラウザーの下部に **ダウンロードした RDP ファイル** へのリンクが表示されます。 

    :::image type="content" source="./media/connect-windows-virtual-machine/rdp-download.png" alt-text="RDP のダウンロード":::                        
1. RDP ファイルを開き、VM の作成時に入力した VM の資格情報を入力します。 これで、Windows VM に接続されているはずです。 

## <a name="next-steps"></a>次の手順
[方法: Linux VM に接続する](connect-linux-virtual-machine.md)

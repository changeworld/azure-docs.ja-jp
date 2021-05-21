---
title: Azure Percept DK に最適な更新パッケージを選択する
description: Azure Percept DK のバージョンを特定し、最適な更新パッケージを選択する方法
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 05/04/2021
ms.custom: template-how-to
ms.openlocfilehash: 6f32e785af7f302124a11613a40e286fe3b641b9
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2021
ms.locfileid: "109488053"
---
# <a name="how-to-determine-and-download-the-best-update-package-for-ota-and-usb-updates"></a>OTA および USB の更新プログラムに最適な更新パッケージを特定してダウンロードする方法

このページでは、開発キットに最適な更新パッケージを選択する方法と、更新パッケージのダウンロード場所に関するガイダンスを提供します。

デバイスを更新する方法の詳細については、次の記事を参照してください。
- [Azure Percept DK を無線で更新する](https://docs.microsoft.com/azure/azure-percept/how-to-update-over-the-air)
- [Azure Percept DK を USB 経由で更新する](https://docs.microsoft.com/azure/azure-percept/how-to-update-via-usb)


## <a name="prerequisites"></a>前提条件

- [設定されて Azure Percept Studio および IoT Hub に接続されている](https://docs.microsoft.com/azure/azure-percept/quickstart-percept-dk-set-up) [Azure Percept DK](https://go.microsoft.com/fwlink/?linkid=2155270)。

## <a name="identify-the-model-name-and-software-version-of-your-dev-kit"></a>開発キットのモデル名とソフトウェア バージョンを特定する
正しい更新パッケージを開発キットに適用するには、まず現在実行されているソフトウェア バージョンを確認する必要があります。

> [!WARNING]
> 正しくない更新パッケージを適用すると、開発キットが操作不能になる可能性があります。 正しい更新パッケージを適用するには、次の手順に従う必要があります。

1. 開発キットの電源をオンにし、それが Azure Percept Studio に接続されていることを確認します。
1. Azure Percept Studio で、左側のメニューから **[デバイス]** を選択します。
1. デバイスの一覧から、現在接続されているデバイスの名前を選択します。 状態は **[接続済み]** と表示されます。
1. **[デバイスを IoT Hub で開く]** を選択します
1. 場合によっては Azure アカウントに再びサインインするよう求められることがあります。
1. **[デバイス ツイン]** を選択します。
1. デバイス ツインのプロパティをスクロールし、 **"deviceInformation"** の下にある **"model"** と **"swVersion"** を見つけて、その値をメモします。

## <a name="determine-the-correct-update-package"></a>正しい更新パッケージを確認する
前のセクションで特定した **model** と **swVersion** を使用して、下の表を確認し、ダウンロードする更新パッケージを決定します。


|model  |swVersion  |更新方法  |ダウンロード リンク  |
|---------|---------|---------|---------|
|PE-101     |2020.108.101.105、 <br>2020.108.114.120、 <br>2020.109.101.122、 <br>2020.109.116.120、 <br>2021.101.106.118        |**USB のみ**         |[USB 更新パッケージ](https://go.microsoft.com/fwlink/?linkid=2155734)         |
|PE-101     |2021.102.108.112、 <br>         |OTA または USB        |[OTA マニフェスト](https://go.microsoft.com/fwlink/?linkid=2155625)<br>[OTA 更新パッケージ](https://go.microsoft.com/fwlink/?linkid=2161538)<br>[USB 更新パッケージ](https://go.microsoft.com/fwlink/?linkid=2155734)          |
|APDK-101     |すべての swVersion        |OTA または USB       | [OTA マニフェスト](https://go.microsoft.com/fwlink/?linkid=2162292)<br>[OTA 更新パッケージ](https://go.microsoft.com/fwlink/?linkid=2161538)<br>[USB 更新パッケージ](https://go.microsoft.com/fwlink/?linkid=2155734)        |



## <a name="next-steps"></a>次のステップ
前のセクションで特定した方法と更新パッケージを使用して、開発キットを更新します。
- [Azure Percept DK を無線で更新する](https://docs.microsoft.com/azure/azure-percept/how-to-update-over-the-air)
- [Azure Percept DK を USB 経由で更新する](https://docs.microsoft.com/azure/azure-percept/how-to-update-via-usb)


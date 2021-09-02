---
title: Azure Percept DK 更新パッケージを選択する
description: Azure Percept DK のバージョンを特定し、最適な更新パッケージを選択する方法
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 07/23/2021
ms.custom: template-how-to
ms.openlocfilehash: e2256ba6ad1f2f125b21e7cb59ab74b44864bce6
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070777"
---
# <a name="how-to-determine-and-download-the-best-update-package-for-ota-and-usb-updates"></a>OTA および USB の更新プログラムに最適な更新パッケージを特定してダウンロードする方法

このページでは、開発キットに最適な更新パッケージを選択する方法と、更新パッケージのダウンロード場所に関するガイダンスを提供します。

デバイスを更新する方法の詳細については、次の記事を参照してください。
- [Azure Percept DK を無線で更新する](./how-to-update-over-the-air.md)
- [Azure Percept DK を USB 経由で更新する](./how-to-update-via-usb.md)


## <a name="prerequisites"></a>前提条件

- [設定されて Azure Percept Studio および IoT Hub に接続されている](./quickstart-percept-dk-set-up.md) [Azure Percept DK](https://go.microsoft.com/fwlink/?linkid=2155270)。

## <a name="identify-the-model-name-and-software-version-of-your-dev-kit"></a>開発キットのモデル名とソフトウェア バージョンを特定する
正しい更新パッケージを開発キットに適用するには、まず現在実行されているソフトウェア バージョンを確認する必要があります。

> [!WARNING]
> 正しくない更新パッケージを適用すると、開発キットが操作不能になる可能性があります。 正しい更新パッケージを適用するには、次の手順に従う必要があります。

オプション 1: 
1. [Azure Percept Studio](./overview-azure-percept-studio.md) にログインします。
2. **[デバイス]** で、開発キット デバイスを選択します。
3. **[全般]** タブで、**モデル** と **SW バージョン** の情報を探します。

オプション 2:
1. **IoT Hub** サービスの **IoT Edge デバイス** を Microsoft Azure portal から表示します。
2. デバイスの一覧から開発キット デバイスを選択します。
3. **[デバイス ツイン]** を選択します。
4. デバイス ツインのプロパティをスクロールし、 **"deviceInformation"** の下にある **"model"** と **"swVersion"** を見つけて、その値をメモします。

## <a name="determine-the-correct-update-package"></a>正しい更新パッケージを確認する
前のセクションで特定した **model** と **swVersion** を使用して、下の表を確認し、ダウンロードする更新パッケージを決定します。


|model  |swVersion  |更新方法  |ダウンロード リンク  |Note  |
|---------|---------|---------|---------|---------|
|PE-101     |すべての swVersion       |**USB のみ**         |[2021.107.129.116 USB 更新パッケージ](https://go.microsoft.com/fwlink/?linkid=2169086)         |7 月リリース (2107)         |
|APDK-101     |2021.106.111.115 より前の swVersion |**USB のみ**         |[2021.107.129.116 USB 更新パッケージ](https://go.microsoft.com/fwlink/?linkid=2169086)         |7 月リリース (2107)         |
|APDK-101     |2021.106.111.115        |OTA または USB       |[2021.107.129.116 OTA 更新パッケージ](https://go.microsoft.com/fwlink/?linkid=2169245)<br>[2021.107.129.116 USB 更新パッケージ](https://go.microsoft.com/fwlink/?linkid=2169086)        |7 月リリース (2107)         |


## <a name="next-steps"></a>次のステップ
前のセクションで特定した方法と更新パッケージを使用して、開発キットを更新します。
- [Azure Percept DK を無線で更新する](./how-to-update-over-the-air.md)
- [Azure Percept DK を USB 経由で更新する](./how-to-update-via-usb.md)

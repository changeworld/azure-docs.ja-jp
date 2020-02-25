---
title: Azure Kinect DK をリセットする
description: Azure Kinect DK デバイスを工場出荷時のイメージにリセットする方法について説明します
author: Teresa-Motiv
ms.author: v-tea
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/11/2020
ms.topic: how-to
keywords: kinect, リセット
ms.custom:
- CI 113492
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: f851579712a9549c88f1ca652aea4f31457da132
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201900"
---
# <a name="reset-azure-kinect-dk"></a>Azure Kinect DK をリセットする

お使いの Azure Kinect DK を工場出荷時のイメージにリセットすることが必要になる場合があります (たとえば、ファームウェアの更新プログラムが正常にインストールされなかった場合)。

1. お使いの Azure Kinect DK の電源をオフにします。 これを行うには、USB ケーブルと電源ケーブルを取り外します。
  ![リセット ボタンを覆うねじの位置を示す図。](media/reset-azure-kinect-dk-diagram.png)
1. リセット ボタンを見つけるには、三脚マウント ロックにあるねじを取り外します。
1. 電源ケーブルを再び接続します。
1. まっすぐに伸ばしたペーパークリップの先端を三脚マウント ロックの空のねじ穴に差し込みます。
1. ペーパークリップを使用して、リセット ボタンを軽く押したままにします。
1. リセット ボタンを押したまま、USB ケーブルを再び接続します。
1. 約 3 秒後、電源インジケーター ライトがアンバーに変わります。 ライトの色が変わったら、リセット ボタンを離します。  
   
   リセット ボタンを離した後、デバイスがリセットされている間は、電源インジケーター ライトが白とアンバーで点滅します。 
1. 電源インジケーター ライトが白で点灯するまで待ちます。
1. 三脚マウント ロックにねじを取り付けてリセット ボタンを覆います。
1. Azure Kinect ビューアーを使用して、ファームウェアがリセットされたことを確認します。 これを行うには、[Azure Kinect ビューアー](azure-kinect-viewer.md)を起動し、 **[Device firmware version info]\(デバイスのファームウェアのバージョン情報\)** を選択して、お使いの Azure Kinect DK にインストールされているファームウェアのバージョンを確認します。

デバイスに最新のファームウェアがインストールされていることを常に確認してください。 最新のファームウェア バージョンを入手するには、Azure Kinect ファームウェア ツールを使用します。 使用中のファームウェアの状態を確認する方法の詳細については、「[デバイスのファームウェアのバージョンを確認する](azure-kinect-firmware-tool.md#check-device-firmware-version)」を参照してください。

## <a name="related-topics"></a>関連トピック

- [Azure Kinect DK について](about-azure-kinect-dk.md)
- [Azure Kinect DK を設定する](set-up-azure-kinect-dk.md)
- [Azure Kinect DK のハードウェアの仕様: 動作環境](hardware-specification.md#operating-environment)
- [Azure Kinect ファームウェア ツール](azure-kinect-firmware-tool.md)
- [Azure Kinect ビューアー](azure-kinect-viewer.md)
- [複数の Azure Kinect DK デバイス間での同期](multi-camera-sync.md)

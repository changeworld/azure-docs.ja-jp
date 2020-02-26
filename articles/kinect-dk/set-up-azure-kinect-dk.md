---
title: クイックスタート - Azure Kinect DK を設定する
description: このクイックスタートでは、Azure Kinect DK ハードウェアを設定する方法について説明します
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: quickstart
ms.date: 02/12/2020
keywords: azure, kinect, dev kit, azure dk, 設定, ハードウェア, クイック, usb, 電源, ビューアー, センサー, ストリーミング, セットアップ, SDK, ファームウェア
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: b33565f319de77385c966f742fa52391f4869eed
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211280"
---
# <a name="quickstart-set-up-your-azure-kinect-dk"></a>クイック スタート:Azure Kinect DK を設定する

このクイックスタートでは、Azure Kinect DK を設定する方法についてガイダンスを提供します。 センサー ストリームの視覚化をテストし、[Azure Kinect ビューアー](azure-kinect-viewer.md)を使用する方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="system-requirements"></a>システム要件

[システム要件](system-requirements.md)をチェックして、ホスト PC の構成が Azure Kinect DK の最小要件をすべて満たしていることを確かめます。

## <a name="set-up-hardware"></a>ハードウェアを設定する

> [!NOTE]
> デバイスを使用する前に、必ずカメラの保護用フィルムを取り外してください。

1. 電源コネクタを、デバイスの背面にある電源ジャックに差し込みます。 USB 電源アダプターをケーブルのもう一端に接続し、アダプターを電源コンセントに差し込みます。
2. USB データ ケーブルをデバイスに接続し、PC の USB 3.0 ポートに接続します。
   >[!NOTE]
   >最適な結果を得るには、デバイスと PC をこのケーブルで直接接続します。 接続する際に延長ケーブルや追加のアダプターを使用することは避けてください。

3. 電源インジケーター LED (USB ケーブルの横) が白であることを確認します。
  
   デバイスの電源がオンになるまでに数秒かかります。 前面の LED ストリーミング インジケーターがオフになっていれば、デバイスを使用する準備ができています。  

   電源インジケーター LED の詳細については、「[ライトの意味](hardware-specification.md#what-does-the-light-mean)」を参照してください。

    ![デバイスのすべての機能](./media/quickstarts/full-device-features.png)

## <a name="download-the-sdk"></a>SDK のダウンロード

1. SDK をダウンロードするには、[こちら](sensor-sdk-download.md)をクリックしてください。
2. お使いの PC に SDK をインストールします。

## <a name="update-firmware"></a>ファームウェアを更新する

正常に機能するためには、SDK に最新バージョンのデバイス ファームウェアが必要です。 ファームウェアのバージョンを確認して更新するには、「[Azure Kinect DK のファームウェアを更新する](update-device-firmware.md)」の手順に従います。

## <a name="verify-that-the-device-streams-data"></a>デバイスでデータがストリーム配信されていることを確認する

1. [Azure Kinect ビューアー](azure-kinect-viewer.md)を起動します。 このツールを起動するには、次のいずれかの方法を使用します。
   - ビューアーは、コマンド ラインから起動することも、実行可能ファイルをダブルクリックして起動することもできます。 ファイル `k4aviewer.exe` は、SDK の tools ディレクトリにあります (例: `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe`。ここで、`X.Y.Z` はインストールされている SDK のバージョンです)。
   - Azure Kinect ビューアーは、デバイスの **[Start]\(開始\)** メニューから起動できます。
2. Azure Kinect ビューアーで、 **[Open Device]\(デバイスを開く\)**  >  **[Start]\(開始\)** を選択します。

    ![Azure Kinect ビューアー](./media/quickstarts/viewer.png)

3. ツールによって各センサー ストリームが視覚化されていることを確認します。
   - 深度カメラ
   - カラー カメラ
   - 赤外線カメラ
   - IMU
   - マイク

    ![視覚化ツール](./media/quickstarts/visualization-tool.png)

Azure Kinect DK のセットアップが完了しました。 これで、アプリケーションの開発やサービスの統合を開始できます。

問題が発生した場合は、[トラブルシューティング](troubleshooting.md)に関するページを確認してください。

## <a name="see-also"></a>関連項目

- [Azure Kinect DK のハードウェア情報](hardware-specification.md)
- [デバイスのファームウェアを更新する](update-device-firmware.md)
- [Azure Kinect ビューアー](azure-kinect-viewer.md)の詳細

## <a name="next-steps"></a>次のステップ

Azure Kinect DK の準備が整って機能するようになったら、次の内容を学習することもできます
> [!div class="nextstepaction"]
> [センサー ストリームをファイルに記録する](record-sensor-streams-file.md)

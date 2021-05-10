---
title: Azure Percept DK コンポーネントを箱から取り出して組み立てる
description: Azure Percept DK を箱から取り出し、接続し、電源を入れる方法について学習します
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/16/2021
ms.custom: template-quickstart
ms.openlocfilehash: efa255ba38f7e00785335bf458ecc0ed91da646b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608182"
---
# <a name="quickstart-unbox-and-assemble-your-azure-percept-dk-components"></a>クイックスタート: Azure Percept DK コンポーネントを箱から取り出して組み立てる

Azure Percept DK を受け取ったら、このガイドを参照して、コンポーネントの接続とデバイスの電源投入に関する情報を確認してください。

## <a name="prerequisites"></a>前提条件

- Azure Percept DK (開発キット)
- P7 ドライバー (必要に応じて、電源ケーブル コネクタをキャリア ボードに固定するために使用します)

## <a name="unbox-and-assemble-your-device"></a>デバイスを箱から取り出して組み立てる

1. Azure Percept DK コンポーネントを箱から取り出します。

    開発キットには、キャリア ボード、Azure Percept Vision SoM、アンテナとケーブルを含むアクセサリ ボックス、および 16 進値のキーが記載されたウェルカム カードが含まれています。

1. 開発キット コンポーネントを接続します。

    > [!NOTE]
    > 電源アダプター ポートは、キャリア ボードの右側にあります。 残りのポート (USB-A x 2、USB-C x 1、およびイーサネット x 1) と電源ボタンは、キャリア ボードの左側にあります。

    1. 両方の Wi-Fi アンテナをキャリア ボードに手でねじ込みます。

    1. Vision SoM を、USB-C ケーブルを使用してキャリア ボードの USB-C ポートに接続します。

    1. 電源ケーブルを電源アダプターに接続します。

    1. デバイスから残りのプラスチック包装材をすべて取り外します。

    1. 電源アダプターおよびケーブルをキャリア ボードとコンセントに接続します。 電源ケーブル コネクタをキャリア ボードに完全に固定するには、P7 ドライバー (開発キットには含まれていません) を使用してコネクタのネジを締めます。

    1. 電源ケーブルをコンセントに接続すると、デバイスの電源が自動的にオンになります。 キャリア ボードの左側にある電源ボタンが点灯します。 デバイスが起動するまでしばらく時間がかかります。

        > [!NOTE]
        > 電源ボタンは、電源コンセントに接続したままでデバイスを電源オフにしたり再起動したりするためのものです。 停電が発生した場合、デバイスは自動的に再起動されます。

開発キット アセンブリの視覚的デモンストレーションについては、次の動画の 0:00 から 0:50 をご覧ください。

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>次のステップ

開発キットが接続され、電源がオンになったので、[Azure Percept DK のセットアップ エクスペリエンスのチュートリアル](./quickstart-percept-dk-set-up.md)のページを参照してデバイスのセットアップを完了してください。 設定エクスペリエンスでは、開発キットを Wi-Fi ネットワークに接続し、SSH ログインを設定し、IoT ハブを作成し、開発キットを自分の Azure アカウントにプロビジョニングします。 デバイスの設定が完了すると、プロトタイプ作成を開始できるようになります。
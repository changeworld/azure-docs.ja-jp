---
title: Azure Percept DK に SSH で接続する
description: PuTTY を使用して Azure Percept DK に SSH 接続する方法について説明します
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 8dda18271de9b7d65246f0882ee7a68191031c05
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096617"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>Azure Percept DK に SSH で接続する

[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) から Azure Percept DK への SSH 接続を設定するには、次の手順に従います。

## <a name="prerequisites"></a>前提条件

- Wi-Fi 機能を備えた Windows、Linux、OS X ベースのホスト コンピューター
- SSH クライアント
    - ホスト コンピューターで Windows を実行している場合、SSH クライアントとしては [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) が最適であり、このガイド全体を通して PuTTY が使用されています。
    - ホスト コンピューターで Linux または OS X が実行されている場合、SSH サービスはオペレーティング システムに含まれているため、個別のクライアント アプリケーションを使用しなくても実行できます。 SSH サービスの実行方法について詳しくは、オペレーティング システムの製品ドキュメントを確認してください。
- Azure Percept DK
- [Azure Percept DK のオンボードエクスペリエンス](./quickstart-percept-dk-set-up.md)中に SSH ログイン アカウントを設定する

## <a name="initiate-the-ssh-connection"></a>SSH 接続を初期化する

1. Azure Percept DK (開発キット) の電源をオンにする

1. 開発キットが既にイーサネットまたは Wi-Fi でネットワークに接続されている場合は、次の手順に進んでください。 それ以外の場合は、他の Wi-Fi ネットワークに接続するときと同じように、開発キットの Wi-Fi アクセス ポイントに直接ホスト コンピューターを接続してください。
    - **ネットワーク名**: .scz-xxxx ("xxxx" は開発キットの MAC ネットワーク アドレスの下 4 桁)
    - **パスワード**: 開発キットに付属のウェルカム カードに記載されています。

    > [!WARNING]
    > Azure Percept DK Wi-Fi のアクセスポイントに接続している間は、ホスト コンピューターのインターネット接続が一時的に失われます。 アクティブなビデオ会議通話や Web ストリーミングといったネットワークベースのエクスペリエンスは、Azure Percept DK のオンボード エクスペリエンスの手順 3. が完了するまで中断されます。

1. PuTTY を開きます。 次のように入力し、 **[Open]\(開く\)** をクリックして、開発キットに SSH 接続します。

    1. ホスト名: 10.1.1.1
    1. ポート: 22
    1. 接続の種類: SSH

    > [!NOTE]
    > **ホスト名** は、デバイスの IP アドレスです。 開発キットの Wi-Fi アクセスポイントに接続している場合、IP アドレスは 10.1.1.1 になります。 イーサネット経由で接続している場合は、デバイスのローカル IP アドレスを使用します。デバイスのローカル IP アドレスは、イーサネット ルーターまたはハブから取得できます。 デバイスが Wi-Fi で接続されている場合、[Azure Percept DK のオンボーディング エクスペリエンス](./quickstart-percept-dk-set-up.md)時に収集した IP アドレスを使用する必要があります。

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="画像。":::

1. オンボード エクスペリエンス中に作成された SSH ユーザー名とパスワードで PuTTY ターミナルにログインします。

## <a name="next-steps"></a>次のステップ

Azure Percept DK への SSH 接続に成功すると、トラブルシューティング、USB の更新、DiagTool ツールの実行、SoftAP ツールの実行など、さまざまなタスクを実行できます。
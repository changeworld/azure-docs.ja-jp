---
title: Azure Percept DK でネットワークの詳細設定を行う
description: この記事では、Azure Percept DK セットアップ エクスペリエンス中のネットワークの詳細設定について説明します
author: WendyAnn0079
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 7/19/2021
ms.custom: template-how-to
ms.openlocfilehash: ac06d331bcd587c1294c21388faac75cc6f8cfd6
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123219633"
---
# <a name="set-up-advanced-network-settings-on-the-azure-percept-dk"></a>Azure Percept DK でネットワークの詳細設定を行う

Azure Percept DK を使用すると、開発キットのさまざまなネットワーク コンポーネントを制御できます。 これは、セットアップ エクスペリエンスのネットワークの詳細設定で行います。 これらの設定にアクセスするには、[セットアップ エクスペリエンスを開始](./quickstart-percept-dk-set-up.md)し、 **[ネットワーク接続]** ページで **[ネットワークの詳細設定へのアクセス]** を選択する必要があります。

:::image type="content" source="media/how-to-set-up-advanced-network-settings/advanced-ns-entry.png" alt-text="[ネットワーク接続] ページからネットワークの詳細設定を起動します":::

## <a name="select-the-security-setting"></a>セキュリティ設定を選択する
IPv4 と IPv6 はどちらも、ローカル接続の Azure Percept DK でサポートされています。

> [!NOTE]
> Azure IoTHub では [IPv6 をサポートしていません](../iot-hub/iot-hub-understand-ip-address.md#support-for-ipv6)。 IoTHub との通信には IPv4 を使用する必要があります。
1. IPv4 オプション ボタンを選択し、[ネットワーク設定] で項目を選択して IPv4 設定を変更します
1. IPv6 オプション ボタンを選択し、[ネットワーク設定] で項目を選択して IPv6 設定を変更します
1. **[ネットワーク設定]** は、選択内容によって変わる可能性があります

:::image type="content" source="media/how-to-set-up-advanced-network-settings/advanced-ns-security.png" alt-text="ネットワーク オプションの一覧を表示するには、セキュリティ プロトコルを選択します":::

## <a name="define-a-static-ip-address"></a>静的 IP アドレスを定義する

1. **[ネットワークの詳細設定]** ページで、一覧から **[静的 IP アドレスを定義する]** を選択します
1. ドロップダウン メニューから **[ネットワーク インターフェイス]** を選択します
1. **[動的 IP アドレス]** をオフにします
1. 静的 IP アドレスを入力します
1. サブネットの IP アドレス (サブネット マスクとも呼ばれます) を入力します
1. ゲートウェイの IP アドレス (既定のゲートウェイとも呼ばれます) を入力します
1. 該当する場合は、DNS アドレスを入力します
1. **[保存]** を選びます。
1. **[戻る]** を選択して、メインの **[ネットワークの詳細設定]** ページに戻ります

## <a name="define-dns-server-for-docker"></a>Docker の DNS サーバーを定義する
これらの設定を使用すると、新しい Docker DNS IP アドレスを変更または追加できます。

> [!NOTE]
> Docker サービスは、IPv4 DNS エントリのみを受け入れるように構成されています。  IPv6 画面から追加されたエントリは無視されます。

1. **[ネットワークの詳細設定]** ページで、一覧から **[Docker の DNS サーバーを定義する]** を選択します
1. Docker IPv4 DNS アドレスを入力します
1. **[保存]** を選びます。
1. **[戻る]** を選択して、メインの **[ネットワークの詳細設定]** ページに戻ります

## <a name="define-bridge-internet-protocol-for-docker"></a>Docker のブリッジ インターネット プロトコルを定義する
[ブリッジ インターネット プロトコル] 画面では、Docker コンテナーの IPv4 アドレス空間を変更できます。

デバイスの IP アドレスが Azure Percept Devkit の Docker サービス (172.17.x.x) と同じルートを共有している場合は、Docker のブリッジを別のものに変更して、Docker コンテナーと Azure IoTHub 間の通信を許可する必要があります。  

1. **[ネットワークの詳細設定]** ページで、一覧から **[Docker のブリッジ インターネット プロトコルを定義する]** を選択します
1. Docker ブリッジ インターネット プロトコル IPv4 アドレス (BIP) を入力します
1. **[保存]** を選びます。
1. **[戻る]** を選択して、メインの **[ネットワークの詳細設定]** ページに戻ります

## <a name="define-an-internet-proxy-server"></a>インターネット プロキシ サーバーを定義する
このオプションを使用すると、プロキシ サーバーを定義できます。    

1. **[ネットワークの詳細設定]** ページで、一覧から **[インターネット プロキシ サーバーを定義する]** を選択します
1. **[プロキシ サーバーを使用する]** チェック ボックスをオンにして、このオプションを有効にします。
1. プロキシ サーバーの **[HTTP アドレス]** を入力します (該当する場合)
1. プロキシ サーバーの **[HTTPS アドレス]** を入力します (該当する場合)
1. プロキシ サーバーの **[FTP アドレス]** を入力します (該当する場合)
1. **[プロキシ アドレスなし]** ボックスに、プロキシ サーバーを使用してはならない IP アドレスを入力します
1. **[保存]** を選びます。
1. **[戻る]** を選択して、メインの **[ネットワークの詳細設定]** ページに戻ります

## <a name="setup-zero-touch-provisioning"></a>ゼロタッチ プロビジョニングを設定する

> [!IMPORTANT]
> **[ゼロタッチ プロビジョニングの設定]** 設定は現在機能していません

このオプションを使用すると、複数のデバイスを一度に Wi-Fi インフラストラクチャにオンボードするために、Azure Percept DK を [Wi-Fi Easy Connect<sup>TM</sup> Bulk Configurator](https://techcommunity.microsoft.com/t5/internet-of-things/simplify-wi-fi-iot-device-onboarding-with-zero-touch/ba-p/2161129#:~:text=A%20Wi-Fi%20Easy%20Connect%E2%84%A2%20Configurator%2C%20paired%20with%20the,device%20to%20any%20WPA2-Personal%20or%20WPA3-Personal%20wireless%20LAN.) に変換することができます。  

## <a name="define-access-point-passphrase"></a>アクセス ポイントのパスフレーズを定義する 
このオプションを使用すると、Azure Percept DK Wi-Fi アクセス ポイントのパスフレーズを更新できます。  

> [!CAUTION]
> 新しいパスフレーズを保存すると、すぐに Wi-Fi アクセス ポイントから切断されます。  アクセスを回復するには、新しいパスフレーズを使用して再接続してください。  

パスフレーズの要件:
- 12 から 123 文字の長さにする必要があります
- 少なくとも 1 つの小文字、1 つの大文字、1 つの数字、1 つの特殊文字を含める必要があります。

1. **[ネットワークの詳細設定]** ページで、一覧から **[アクセス ポイントのパスフレーズを定義する]** を選択します
1. 新しいパスワードを入力します
1. **[保存]** を選びます。
1. **[戻る]** を選択して、メインの **[ネットワークの詳細設定]** ページに戻ります

## <a name="next-steps"></a>次の手順
**[ネットワークの詳細設定]** の変更が完了したら、 **[戻る]** ボタンを選択して、[Azure Percept DK セットアップ エクスペリエンスを続行します](./quickstart-percept-dk-set-up.md)。
---
title: Microsoft Azure Stack Edge Mini R の技術仕様とコンプライアンス | Microsoft Docs
description: Azure Stack Edge Mini R デバイスの技術仕様とコンプライアンスについて説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 06/29/2021
ms.author: alkohli
ms.openlocfilehash: 8b2da6b75ec495fe96b0c65e312a91d0dd30a2d2
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113091503"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>Azure Stack Edge Mini R の技術仕様

Microsoft Azure Stack Edge Mini R デバイスのハードウェア コンポーネントは、この記事で説明されている技術的仕様と規制標準に準拠します。 技術仕様には、CPU、メモリ、電源装置 (PSU)、ストレージ容量、エンクロージャの寸法と重量が記載されています。


## <a name="compute-memory"></a>コンピューティング、メモリ

Azure Stack Edge Mini R デバイスには、コンピューティングとメモリについて次のような仕様があります。

| 仕様           | 値                           |
|-------------------------|---------------------------------|
| CPU の種類                | Intel Xeon-D 1577               |
| CPU: Raw                | 合計コア数 16、合計 vCPU 数 32  |
| CPU: 使用可能             | vCPU 24 個                        |
| メモリの種類             | 16 GB 2400 MT/s SODIMM          |
| メモリ: Raw             | 48 GB RAM (3 x 16 GB)           |
| メモリ: 使用可能          | 32 GB RAM                       |


## <a name="compute-acceleration"></a>コンピューティング アクセラレーション

Vision Processing Unit (VPU) がすべての Azure Stack Edge Mini R デバイスに搭載されており、これにより Kubernetes、ディープ ニューラル ネットワーク、Computer Vision ベースのアプリケーションが使用可能になります。

| 仕様             | 値                  |
|---------------------------|------------------------|
| コンピューティング アクセラレーション カード | Intel Movidius Myriad X VPU <br> 詳細については、「[Intel Movidius Myriad X VPU](https://www.movidius.com/MyriadX)」をご覧ください。 |


## <a name="storage"></a>Storage

Azure Stack Edge Mini R デバイスには、1 つのデータ ディスクと (オペレーティング システムの記憶域として機能する) 1 つのブート ディスクがあります。 次の表にデバイスのストレージ容量の詳細を示します。

|     仕様                          |     値                                              |
|--------------------------------------------|--------------------------------------------------------|
|    ソリッド ステート ドライブ (SSD) の数     |    2 x 1 TB ディスク <br> 1 つのデータ ディスクと 1 つのブート ディスク |
|    単一の SSD 容量                     |    1 TB (テラバイト)                                                |
|    合計容量 (データのみ)              |    1 TB (テラバイト)                                                |
|    合計の使用可能な容量*                  |    最大 750 GB                                            |

*容量の一部は内部仕様のために予約されています。*

## <a name="network"></a>ネットワーク

Azure Stack Edge Mini R デバイスのネットワークに関する仕様は次のとおりです。

|仕様         |値                                                               |
|----------------------|--------------------------------------------------------------------|
|ネットワーク インターフェイス    |2 x 10 Gbps SFP+ <br> ローカル UI にはポート 3 とポート 4 として表示されます。    |
|ネットワーク インターフェイス    |2 x 1 Gbps RJ45 <br> ローカル UI にはポート 1 とポート 2 として表示されます。     |
|Wi-Fi                 |802.11ac                                                            |

## <a name="routers-and-switches"></a>ルーターとスイッチ

次のルーターとスイッチは、Azure Stack Edge Mini R デバイスで 10 Gbps SPF+ ネットワーク インターフェイス (ポート 3 およびポート 4) と互換性があります。

|ルーター/スイッチ     |メモ                         |
|------------------|------------------------------|
|[VoyagerESR 2.0](https://klastelecom.com/products/voyageresr2-0/)    |Cisco ESS3300 スイッチ コンポーネント   |
|[VoyagerSW26G](https://klastelecom.com/products/voyagersw26g/)       |                                 |
|[VoyagerVM 3.0](https://klastelecom.com/products/voyager-vm-3-0/)    |                                 |
|[TDC スイッチ](https://klastelecom.com/voyager-tdc/)                   |                                 |
|[TRX R2](https://klastelecom.com/products/trx-r2/) (8 コア)  <!--Better link: https://www.klasgroup.com/products/voyagersw12gg/? On current link target, an "R6" link opens this page.-->        |                              |
|[SW12GG](https://www.klasgroup.com/products/voyagersw12gg/)          |                                 |

## <a name="transceivers-cables"></a>トランシーバー、ケーブル

Azure Stack Edge Mini R デバイスでは、次の銅線 SFP+ (10 Gbps) トランシーバーとケーブルを使用することを強くお勧めします。 互換性のある光ファイバー ケーブルは SFP+ ネットワーク インターフェイス (ポート 3 およびポート 4) で使用できますが、テストは行われていません。

|SFP+ トランシーバー タイプ |サポートされているケーブル    | メモ |
|----------------------|--------------------|-------|
|SFP+ 直接接続銅線 (10GSFP+Cu)| <ul><li>[FS SFP-10G-DAC](https://www.fs.com/c/fs-10g-sfp-dac-1115) (カスタム オーダーとして、工業温度 -40ºC から +85ºC まで使用可能)</li><br><li>[10Gtek CAB-10GSFP-P0.5M](http://www.10gtek.com/10G-SFP+-182)</li><br><li>[Cisco SFP-H10GB-CU1M](https://www.cisco.com/c/en/us/products/collateral/interfaces-modules/transceiver-modules/data_sheet_c78-455693.html)</li></ul> |<ul><li>SFP+ Twinax DAC ケーブルとも呼ばれています。</li><br><li>電力使用量が最も少なく、最もシンプルであるため、オプションとしてお勧めします。</li><br><li>オートネゴシエーションはサポートされていません。</li><br><li>SFP+ デバイスへの SFP デバイスの接続はサポートされていません。</li></ul>|

## <a name="power-supply-unit"></a>電源装置ユニット

Azure Stack Edge Mini R デバイスには、電源を供給し、オンボード バッテリを充電するための外部 85 W AC アダプターが付属しています。

次の表に電源装置の仕様を示します。

| 仕様           | 値                      |
|-------------------------|----------------------------|
| 最大出力電力    | 85 W                       |
| 頻度               | 50/60 Hz                   |
| 選択電圧範囲 | オート レンジ: 100-240 V AC |

## <a name="included-battery"></a>付属のバッテリ

Azure Stack Edge Mini R デバイスには、電源装置によって充電されるオンボード バッテリも搭載されています。

追加の [2590 型バッテリ](https://www.bren-tronics.com/bt-70791ck.html)をオンボード バッテリと組み合わせて使用すると、充電までのデバイスの使用時間を延ばすことができます。 このバッテリは、使用する国で適用される安全性、輸送、および環境のすべての規制に準拠している必要があります。

| 仕様            | 値      |
|--------------------------|------------|
| オンボード バッテリの容量 | 73 Wh      |

## <a name="enclosure-dimensions-and-weight"></a>エンクロージャの寸法と重量

次の表に、エンクロージャの寸法と重量の各種仕様を示します。

### <a name="enclosure-dimensions"></a>エンクロージャの寸法

次の表に、ラグド ケースを含むデバイスおよび USP の寸法を示します (ミリメートルおよびインチ単位)。

|     エンクロージャ     |     mm     |     インチ     |
|-------------------|---------------------|----------------|
|    [高さ]         |    68               |    2.68        |
|    幅          |    208              |    8.19        |
|    長さ         |    259              |    10.20       |


### <a name="enclosure-weight"></a>エンクロージャの重量

次の表に、バッテリを含むデバイスの重量を示します。

|     エンクロージャ                     |     Weight          |
|-----------------------------------|---------------------|
|    デバイスの総重量     |     7 ポンド           |

## <a name="enclosure-environment"></a>エンクロージャ環境

ここでは、温度、湿度、高度など、エンクロージャの環境に関連する仕様を示します。

|     仕様             |     説明                                                          |
|--------------------------------|--------------------------------------------------------------------------|
|     温度範囲          |     0 – 40° C (運用時)                                              |
|     振動                  |     MIL-STD-810 Method 514.7*<br> Procedure I CAT 4, 20                  |
|     衝撃                      |     MIL-STD-810 Method 516.7*<br> Procedure IV, Logistic                 |
|     高度                   |     運用時:   15,000 フィート<br> 非運用時:40,000 フィート          |

"**すべての参照は MIL-STD-810G Change 1 (2014) です*"

## <a name="next-steps"></a>次の手順

- [Azure Stack Edge Mini R をデプロイする](azure-stack-edge-placeholder.md)

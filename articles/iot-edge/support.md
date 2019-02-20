---
title: サポートされているオペレーティング システム、コンテナー エンジン - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge デーモンとランタイムを実行できるオペレーティング システム、運用デバイス用にサポートされるコンテナー エンジンについて説明します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/17/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: d8059ac4965ce5582b899ebc0d765e00ae9deb35
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55892798"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge のサポートされるシステム

Azure IoT Edge 製品のサポートを受ける方法は複数あります。

**バグの報告** – Azure IoT Edge 製品に関する開発の大多数は、IoT Edge のオープン ソース プロジェクトで発生します。 バグはプロジェクトの[問題ページ](https://github.com/azure/iotedge/issues)で報告できます。 修正プログラムはプロジェクトが製品の更新プログラムになるまでの時間を早めます。

**Microsoft カスタマー サポート チーム** – [サポート プラン](https://azure.microsoft.com/support/plans/)に加入しているユーザーは、[Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac) から直接サポート チケットを作成することで、Microsoft カスタマー サポート チームとやり取りをすることができます。

**機能の要望** – Azure IoT Edge 製品はその製品の[ユーザーの声のページ](https://feedback.azure.com/forums/907045-azure-iot-edge)を介して機能の要望を追跡します。

## <a name="container-engines"></a>コンテナー エンジン
Azure IoT Edge には、モジュールを起動するためのコンテナー エンジンが必要です。これは、モジュールがコンテナーとして実装されるためです。 Microsoft には、この要件を満たすために、moby-engine というコンテナー エンジンが用意されています。 これは Moby オープン ソース プロジェクトをベースとします。 他にも有名なコンテナー エンジンとして、Docker CE や Docker EE が挙げられます。 これらも Moby オープン ソース プロジェクトをベースとし、Azure IoT Edge と互換性があります。 Microsoft はこれらのコンテナー エンジンを使用するシステムに対してベスト エフォート サポートを提供しています。ただし、Microsoft はそれらの問題について修正プログラムを発行することができません。 この理由から、Microsoft では運用システムで moby-engine を使用することを推奨しています。

<br>
<center>
![コンテナー ランタイムとしての Moby](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>オペレーティング システム
Azure IoT Edge はコンテナーを実行できるほとんどのオペレーティング システムで実行できます。ただし、それらがすべて均等にサポートされているわけではありません。 オペレーティング システムは、ユーザーが受けられるサポートのレベルを表す階層別にグループ化されています。
* レベル 1 のシステムは公式にサポートされていると見なされます。 これは、次のことを意味します。
    * Microsoft がそのオペレーティング システムに対して自動テストを実施している
    * Microsoft がそれらのインストール パッケージを提供している
* レベル 2 のシステムは Azure IoT Edge と互換性があり、比較的簡単に使用できると見なされます。 これは、次のことを意味します。
    * Microsoft がそのプラットフォームに対してアドホック テストを実施している、またはそのプラットフォーム上で Azure IoT Edge をパートナーが正常に実行していることを把握している
    * 他のプラットフォーム用のインストール パッケージがそれらのプラットフォームで機能することがある
    
ホスト OS のファミリは、モジュールのコンテナー内で使用されるゲスト OS のファミリと常に一致する必要があります。 つまり、Linux コンテナーは Linux 上でのみ、Windows コンテナーは Windows 上でのみ使用できます。 Windows を使用している場合は、プロセス分離コンテナーのみがサポートされます (hyper-v 分離コンテナーはサポートされません)。  

<br>
<center>
![ホスト OS とゲスト OS は一致する](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>レベル 1
一般公開
| オペレーティング システム | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Raspbian-stretch | いいえ  | はい|
| Ubuntu Server 16.04 | はい | いいえ  |
| Ubuntu Server 18.04 | はい | いいえ  |

パブリック プレビュー
| オペレーティング システム | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Windows 10 IoT Core ビルド 17763 | はい | いいえ  |
| Windows 10 IoT Enterprise ビルド 17763 | はい | いいえ  |
| Windows Server 2019 | はい | いいえ  |

上記の Windows OS は、Windows 上で Windows コンテナーを実行するデバイスの要件です。 これは、運用環境でサポートされる唯一の構成です。 Windows 用の Azure IoT Edge インストール パッケージを使用すると、Windows 上で Linux コンテナーを使用できます。ただし、これは開発およびテスト専用です。 Windows 上で Linux コンテナーを使用することは、運用環境でサポートされる構成ではありません。 この開発シナリオに使用できるのは、Windows 10 ビルド 14393 以降と、Windows Server 2016 以降のバージョンです。

### <a name="tier-2"></a>レベル 2

| オペレーティング システム | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| CentOS 7.5 | はい | はい |
| Debian 8 | はい | はい |
| Debian 9 | はい | はい |
| RHEL 7.5 | はい | はい |
| Ubuntu 18.04 | はい | はい |
| Ubuntu 16.04 | はい | はい |
| Wind River 8 | はい | いいえ  |
| Yocto | はい | いいえ  |


## <a name="virtual-machines"></a>Virtual Machines
Azure IoT Edge は仮想マシンで実行できます。 これは、エッジ インテリジェンスで既存のインフラストラクチャを拡張しようとする場合によく用いられます。 ホスト VM OS のファミリは、モジュールのコンテナー内で使用されるゲスト OS のファミリと一致する必要があります。 これは、Azure IoT Edge がデバイス上で直接実行される場合と同じ要件です。 Azure IoT Edge は基盤となる仮想化テクノロジに依存しており、Hyper-V や vSphere などのプラットフォームを使用した VM で動作します。

<br>
<center>
![VM 内の Azure IoT Edge](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>最小システム要件
Azure IoT Edge は、Raspberry Pi3 のような小規模なデバイスから、サーバー グレード ハードウェアまで、幅広いデバイスで快適に動作します。 シナリオに最適なハードウェアを選択する際は、実行するワークロードが重要な判断基準となります。 デバイスを最終的に決定するまでには複雑なプロセスを要する場合もありますが、従来型のラップトップやデスクトップ上でソリューションのプロトタイプを作成することは簡単に開始できます。

プロトタイプ作成の過程で得られた経験は、最終的なデバイスの選択に役立ちます。 考慮するべきニーズとしては、次のようなことが挙げられます: ワークロードを構成するモジュールの数、モジュールのコンテナーが共有するレイヤーの数、モジュールの記述に使用される言語、モジュールで処理するデータの量、ワークロードを高速化するためにモジュールに必要となる特殊なハードウェア、ソリューションに求められるパフォーマンス特性、ハードウェアの予算。

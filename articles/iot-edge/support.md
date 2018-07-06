---
title: Azure IoT Edge プラットフォームのサポート | Microsoft Docs
description: Azure IoT Edge によってサポートされるプラットフォーム
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 05a571c4491122ec5c7c35f6bccc4b8c332a4be2
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130711"
---
# <a name="azure-iot-edge-support"></a>Azure IoT Edge のサポート
Azure IoT Edge 製品のサポートを受ける方法は複数あります。

**バグの報告** – Azure IoT Edge 製品に関する開発の大多数は、IoT Edge のオープン ソース プロジェクトで発生します。 バグはプロジェクトの[問題ページ](https://github.com/azure/iotedge/issues)で報告できます。 修正プログラムはプロジェクトが製品の更新プログラムになるまでの時間を早めます。

**Microsoft カスタマー サポート チーム** – [サポート プラン](https://azure.microsoft.com/support/plans/)に加入しているユーザーは、[Azure Portal]( https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac) から直接サポート チケットを作成することで、Microsoft カスタマー サポート チームとやり取りをすることができます。

**機能の要望** – Azure IoT Edge 製品はその製品の[ユーザーの声のページ](https://feedback.azure.com/forums/907045-azure-iot-edge)を介して機能の要望を追跡します。

## <a name="operating-systems"></a>オペレーティング システム
Azure IoT Edge はコンテナーを実行できるほとんどのオペレーティング システムで実行できます。ただし、それらがすべて均等にサポートされているわけではありません。 オペレーティング システムは、ユーザーが受けられるサポートのレベルを表す階層別にグループ化されています。

### <a name="tier-1"></a>レベル 1
レベル 1 のシステムは公式にサポートされていると見なされます。 これは、次のことを意味します。
* Microsoft がそれらのオペレーティング システムに対して自動テストを実施している
* Microsoft がそれらのインストール パッケージを提供している

一般公開
* Ubuntu Server 18.04
* Ubuntu Server 16.04
* Raspbian-stretch

パブリック プレビュー
* Windows 10 Server 1803
* Windows 10 IoT Enterprise (2018 年 4 月の更新プログラム適用済み)
* Windows 10 IoT Core (2018 年 4 月の更新プログラム適用済み)

### <a name="tier-2"></a>レベル 2
レベル 2 のシステムは Azure IoT Edge と互換性があり、比較的簡単に使用できると見なされます。 これは、次のことを意味します。
* Microsoft がそのプラットフォームに対してアドホック テストを実施している、またはそのプラットフォーム上で Azure IoT Edge をパートナーが正常に実行していることを把握している
* 他のプラットフォーム用のインストール パッケージがそれらのプラットフォームで機能することがある

Ubuntu 18.04

Ubuntu 16.04

Wind River 8

Yocto

Debian

Mac

## <a name="container-engines"></a>コンテナー エンジン
Azure IoT Edge がモジュールを起動するには、それが実行されているオペレーティング システムに関係なく、コンテナー エンジンが必要です。 Microsoft には、この要件を満たすために、moby-engine というコンテナー エンジンが用意されています。 これは Moby オープン ソース プロジェクトをベースとします。 他にも有名なコンテナー エンジンとして、Docker CE や Docker EE が挙げられます。 これらも Moby オープン ソース プロジェクトをベースとし、Azure IoT Edge と互換性があります。 Microsoft はこれらのコンテナー エンジンを使用するシステムに対してベスト エフォート サポートを提供しています。ただし、Microsoft はそれらの問題について修正プログラムを発行することができません。 この理由から、Microsoft では運用システムで moby-engine を使用することを推奨しています。


<!-- Links -->
[lnk-edge-blog]: https://azure.microsoft.com/blog/securing-the-intelligent-edge/ 

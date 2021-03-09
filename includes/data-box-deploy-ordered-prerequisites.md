---
title: 同じファイルの 2 つのタブによって共有された前提条件のインクルード ファイル | Microsoft Docs
description: Azure Data Box デプロイの順序付けされた前提条件
services: databox
author: priestlg
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 06/15/2020
ms.author: v-grpr
ms.openlocfilehash: 6aaf57d9bcdfb1f350e1d54937e9c705dd32116e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "85392482"
---
### <a name="for-service"></a>サービスでは

[!INCLUDE [Data Box service prerequisites](data-box-supported-subscriptions.md)]

### <a name="for-device"></a>デバイスでは

開始する前に次の点を確認します。

* データセンター ネットワークに接続されているホスト コンピューターがあること。 Data Box は、このコンピューターからデータをコピーします。 ホスト コンピューターでは、「[Azure Data Box system requirements](../articles/databox/data-box-system-requirements.md)」(Azure Data Box のシステム要件) に記載されている、サポートされるオペレーティング システムが実行されている必要があります。
* お客様のデータセンターには、高速ネットワークが必要です。 10 GbE 接続を少なくとも 1 つ利用することを強くお勧めします。 10 GbE 接続を利用できない場合は、1 GbE データ リンクを使用できますが、コピーの速度が影響を受けます。

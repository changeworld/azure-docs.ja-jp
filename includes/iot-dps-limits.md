---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2b8d60da507aa1eb312d1a8505f756ff337085ff
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77592428"
---
次の表は、Azure IoT Hub Device Provisioning Service のリソースに適用される制限の一覧です。

| リソース | 制限 |
| --- | --- |
| Azure サブスクリプションあたりのデバイス プロビジョニング サービスの最大数 | 10 |
| 加入の最大数 | 1,000,000 |
| 登録の最大数 | 1,000,000 |
| 加入グループの最大数 | 100 |
| CA の最大数 | 25 |
| リンクされた IoT Hub の最大数 | 50 |
| メッセージの最大サイズ | 96 KB|

> [!NOTE]
> お使いのプロビジョニング サービスの加入数と登録数を増やすには、[Microsoft サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。

> [!NOTE]
> CA の最大数を増やすことはサポートされていません。

Device Provisioning Service は、次のクォータを超えた場合に、要求を調整します。

| スロットル | ユニットあたりの値 |
| --- | --- |
| 操作 | 200/分/サービス |
| デバイス登録 | 200/分/サービス |
| デバイスのポーリング操作 | 5/10 秒/デバイス |

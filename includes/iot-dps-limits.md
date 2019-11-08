---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 4bd890adcaa4982c52366faed0f2975729290360
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612107"
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
> お使いのサブスクリプションのインスタンス数を増やすには、[Microsoft サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。

> [!NOTE]
> お使いのプロビジョニング サービスの加入数と登録数を増やすには、[Microsoft サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。

Device Provisioning Service は、次のクォータを超えた場合に、要求を調整します。

| スロットル | ユニットあたりの値 |
| --- | --- |
| Operations | 200/分/サービス |
| デバイス登録 | 200/分/サービス |
| デバイスのポーリング操作 | 5/10 秒/デバイス |

---
author: anastasia-ms
ms.service: iot-dps
ms.topic: include
ms.date: 10/10/2021
ms.author: v-stharr
ms.openlocfilehash: ac1d2554df675dc055530ab4b721a0510f2fddfb
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399403"
---
> [!NOTE]
> このサービスの一部の領域には、調整可能な制限があります。 これは、次の表の *調整可能?* 列に示されています。 制限が調整可能な場合、*調整可能?* の値は *はい* になります。
>
>制限を調整できる実際の値は、各顧客のデプロイによって異なる場合があります。 非常に大規模なデプロイでは、DPS の複数のインスタンスが必要になることがあります。
>
> 調整可能な制限を上げること、または既定の制限を超えたクォータが必要な場合は、[サポート チケットを開いて](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)追加のリソースを要求できます。

次の表は、Azure IoT Hub Device Provisioning Service のリソースに適用される制限の一覧です。

| リソース | 制限 | 調整可能? |
| --- | --- | --- |
| Azure サブスクリプションあたりのデバイス プロビジョニング サービスの最大数 | 10 | はい |
| 登録の最大数 | 1,000,000 | はい |
| 個々の加入の最大数 | 1,000,000 | はい |
| 加入グループの最大数 *(X.509 証明書)* | 100 | はい |
| 加入グループの最大数 *(対称キー)* | 100 | いいえ |
| CA の最大数 | 25 | いいえ |
| リンクされた IoT Hub の最大数 | 50 | いいえ |
| メッセージの最大サイズ | 96 KB| いいえ |

> [!TIP]
> 対称キー加入グループのハード制限がブロックの問題である場合は、個々の加入を回避策として使用することをお勧めします。

Device Provisioning Service には、次のレートの制限があります。

| 料金 | ユニットあたりの値 | 調整可能? |
| --- | --- | --- |
| 操作 | 200/分/サービス | はい |
| デバイス登録 | 200/分/サービス | はい |
| デバイスのポーリング操作 | 5/10 秒/デバイス | いいえ |


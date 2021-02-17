---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 02/05/2021
ms.openlocfilehash: b06b91e972fd07543cf02105360cb0400ef6b0f1
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831547"
---
IoT Edge エージェント ランタイムの応答を使用すると、コンピューティング関連のエラーをトラブルシューティングできます。 考えられる応答の一覧を以下に示します。

* 200 - OK
* 400 - デプロイ構成が正しくない形式であるか、無効です。
* 417 - デバイスにデプロイ構成セットがありません。
* 412 - デプロイ構成にあるスキーマ バージョンが無効です。
* 406 - IoT Edge デバイスがオフラインであるか、状態レポートを送信していません。
* 500 - IoT Edge ランタイムでエラーが発生しました。

詳細については、「[IoT Edge エージェント](/azure/iot-edge/iot-edge-runtime?view=iotedge-2018-06&preserve-view=true#iot-edge-agent)」を参照してください。

次のエラーは、ご使用の Azure Stack Edge Pro の IoT Edge サービスに関連しています<!--/ Data Box Gateway--> すばやく答えを得ることができます。

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Compute modules have Unknown status and can't be used (コンピューティング モジュールの状態が不明であるため、使用できません)

#### <a name="error-description"></a>エラーの説明

デバイス上のすべてのモジュールは状態が不明と示されているため、使用できません。 不明の状態は再起動しても保持されます。<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>推奨されている解決方法

IoT Edge サービスを削除してから、モジュールを再デプロイしてください。 詳細については、「[IoT Edge サービスの削除](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service)」を参照してください。

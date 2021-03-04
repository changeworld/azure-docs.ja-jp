---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 02/05/2021
ms.openlocfilehash: ad981264a99bd48e27f745a789ebe857b7f17d80
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750199"
---
IoT Edge エージェント ランタイムの応答を使用すると、コンピューティング関連のエラーをトラブルシューティングできます。 考えられる応答の一覧を以下に示します。

* 200 - OK
* 400 - デプロイ構成が正しくない形式であるか、無効です。
* 417 - デバイスにデプロイ構成セットがありません。
* 412 - デプロイ構成にあるスキーマ バージョンが無効です。
* 406 - IoT Edge デバイスがオフラインであるか、状態レポートを送信していません。
* 500 - IoT Edge ランタイムでエラーが発生しました。

詳細については、「[IoT Edge エージェント](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent)」を参照してください。

次のエラーは、ご使用の Azure Stack Edge Pro の IoT Edge サービスに関連しています<!--/ Data Box Gateway--> すばやく答えを得ることができます。

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Compute modules have Unknown status and can't be used (コンピューティング モジュールの状態が不明であるため、使用できません)

#### <a name="error-description"></a>エラーの説明

デバイス上のすべてのモジュールは状態が不明と示されているため、使用できません。 不明の状態は再起動しても保持されます。<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>推奨されている解決方法

IoT Edge サービスを削除してから、モジュールを再デプロイしてください。 詳細については、「[IoT Edge サービスの削除](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service)」を参照してください。
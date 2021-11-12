---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6bc968f7763131c4c12e7d523619029698e4ac09
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131579344"
---
## <a name="create-a-device-provisioning-service-enrollment"></a>Device Provisioning Service 登録の作成

TPM のプロビジョニング情報を使用して、Device Provisioning Service で個別登録を作成します。

Device Provisioning Service 内に登録を作成するときに、**デバイス ツインの初期状態** を宣言する機会があります。 デバイス ツインでは、ソリューションで使用される任意のメトリック (リージョン、環境、場所、デバイスの種類など) によってデバイスをグループ化するためのタグを設定できます。 これらのタグは、[自動展開](../articles/iot-edge/how-to-deploy-at-scale.md)を作成するために使用されます。

> [!TIP]
> この記事の手順は Azure portal 向けですが、Azure CLI を使用して個別登録を作成することもできます。 詳細については、[az iot dps enrollment](/cli/azure/iot/dps/enrollment) を参照してください。 この CLI コマンドの一部として、**edge-enabled** フラグを使用して、登録の対象が IoT Edge デバイスであることを指定します。

1. [Azure portal](https://portal.azure.com) で、IoT Hub Device Provisioning Service のインスタンスに移動します。

1. **[設定]** の下の **[登録の管理]** を選択します。

1. **[個別登録の追加]** を選択し、登録を構成する次の手順を完了します。

   1. **[メカニズム]** で **[TPM]** を選択します。

   1. VM または物理デバイスからコピーした **保証キー** と **登録 ID** を指定します。

   1. 必要に応じて、デバイス ID を指定します。 デバイス ID を指定しなかった場合は、登録 ID が使用されます。

   1. **[True]** を選択し、VM または物理デバイスが IoT Edge デバイスであることを宣言します。

   1. デバイスの接続先になるリンクされた IoT ハブを選択するか、 **[Link to new IoT Hub]\(新しい IoT ハブへのリンク\)** を選択します。 複数のハブを選択でき、デバイスは、選択した割り当てポリシーに従ってそれらのハブの 1 つに割り当てられます。

   1. 必要に応じて、 **[デバイス ツインの初期状態]** にタグ値を追加します。 タグを使用して、デバイス グループをモジュール展開のターゲットにすることができます。 詳細については、[大規模な IoT Edge モジュールの展開](../articles/iot-edge/how-to-deploy-at-scale.md)に関する記事を参照してください。

   1. **[保存]** を選択します。

これで、このデバイスの登録が存在しているので、IoT Edge ランタイムによってインストール時にデバイスを自動的にプロビジョニングできます。

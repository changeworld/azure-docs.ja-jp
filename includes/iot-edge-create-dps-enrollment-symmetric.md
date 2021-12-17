---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 92c933cf2f504459359652124f72ca959241db92
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505949"
---
## <a name="create-a-dps-enrollment"></a>DPS の登録を作成する

DPS を介して 1 つ以上のデバイスをプロビジョニングする登録を作成します。

単一の IoT Edge デバイスをプロビジョニングする方法を求めている場合は、**個別の登録** を作成します。 複数のデバイスをプロビジョニングする必要がある場合は、DPS **グループ登録** を作成する手順に従います。

DPS 内に登録を作成するときに、**デバイス ツインの初期状態** を宣言する機会があります。 デバイス ツインでは、ソリューションで必要な任意のメトリック (リージョン、環境、場所、デバイスの種類など) によってデバイスをグループ化するためのタグを設定できます。 これらのタグは、[自動展開](../articles/iot-edge/how-to-deploy-at-scale.md)を作成するために使用されます。

デバイス プロビジョニング サービスでの登録の詳細については、[デバイス登録の管理方法](../articles/iot-dps/how-to-manage-enrollments.md)に関するページをご覧ください。

# <a name="individual-enrollment"></a>[個別加入](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>DPS 個別登録を作成する

> [!TIP]
> この記事の手順は Azure portal 向けですが、Azure CLI を使用して個別登録を作成することもできます。 詳細については、[az iot dps enrollment](/cli/azure/iot/dps/enrollment) を参照してください。 この CLI コマンドの一部として、**edge-enabled** フラグを使用して、登録の対象が IoT Edge デバイスであることを指定します。

1. [Azure portal](https://portal.azure.com) で、IoT Hub デバイス プロビジョニング サービスのインスタンスに移動します。

1. **[設定]** の下の **[登録の管理]** を選択します。

1. **[Add individual enrollment]\(個別登録の追加\)** を選択し、登録を構成する次の手順を完了します。  

   1. **[メカニズム]** で **[対称キー]** を選択します。

   1. デバイスの一意の **登録 ID** を指定します。

   1. 必要に応じて、デバイスの **IoT Hub デバイス ID** を指定します。 デバイス ID を使用して、個々のデバイスをモジュール展開のターゲットにすることができます。 デバイス ID を指定しなかった場合は、登録 ID が使用されます。

   1. **[True]** を選択して、その登録が IoT Edge デバイス用のものであることを宣言します。

   1. 必要に応じて、 **[デバイス ツインの初期状態]** にタグ値を追加します。 タグを使用して、デバイス グループをモジュール展開のターゲットにすることができます。 次に例を示します。

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. **[保存]** を選択します。

1. IoT Edge ランタイムのインストール時に使用する個別登録の **主キー** の値をコピーします。

これで、このデバイスの登録が存在しているので、IoT Edge ランタイムによってインストール時にデバイスを自動的にプロビジョニングできます。

# <a name="group-enrollment"></a>[グループ登録](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>DPS グループ登録を作成する

> [!TIP]
> この記事の手順は Azure portal 向けですが、Azure CLI を使用してグループ登録を作成することもできます。 詳細については、[az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group) を参照してください。 この CLI コマンドの一部として、**edge-enabled** フラグを使用して、登録の対象が IoT Edge デバイスであることを指定します。 グループ登録の場合、すべてのデバイスを IoT Edge デバイスにする必要があります。そうしない場合、いずれも IoT Edge デバイスにすることはできません。

1. [Azure portal](https://portal.azure.com) で、IoT Hub デバイス プロビジョニング サービスのインスタンスに移動します。

1. **[設定]** の下の **[登録の管理]** を選択します。

1. **[Add individual enrollment]\(個別登録の追加\)** を選択し、登録を構成する次の手順を完了します。  

   1. **[グループ名]** を指定します。

   1. 構成証明の種類として **[対称キー]** を選択します。

   1. **[True]** を選択して、その登録が IoT Edge デバイス用のものであることを宣言します。 グループ登録の場合、すべてのデバイスを IoT Edge デバイスにする必要があります。そうしない場合、いずれも IoT Edge デバイスにすることはできません。

   1. 必要に応じて、 **[デバイス ツインの初期状態]** にタグ値を追加します。 タグを使用して、デバイス グループをモジュール展開のターゲットにすることができます。 次に例を示します。

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. **[保存]** を選択します。

1. グループ登録で使用するデバイス キーを作成するときに使用する、登録グループの **主キー** の値をコピーします。

これで、登録グループが存在しているので、IoT Edge ランタイムによってインストール時にデバイスを自動的にプロビジョニングできます。

#### <a name="derive-a-device-key"></a>デバイス キーを派生させる

グループ登録の一部としてプロビジョニングされる各デバイスには、プロビジョニング中に登録で対称キー構成証明を実行するために、派生デバイス キーが必要です。

デバイス キーを生成するには、DPS 登録グループからコピーしたキーを使用して、デバイスに対する一意の登録 ID の [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) を計算し、結果を Base64 形式に変換します。

> [!IMPORTANT]
> デバイス コードには、登録の主キーやセカンダリ キーを含めないでください。

Windows では、次の例に示すように、PowerShell を使用して派生デバイス キーを生成することができます。

**KEY** の値を、前に書き留めた **主キー** で置き換えます。

**REG_ID** の値をデバイスの登録 ID に置き換えます。

```powershell
$KEY='PASTE_YOUR_ENROLLMENT_KEY_HERE'
$REG_ID='PASTE_YOUR_REGISTRATION_ID_HERE'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

派生デバイス キーの出力例を次に示します。

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

---

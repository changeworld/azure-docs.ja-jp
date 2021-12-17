---
title: Microsoft Azure IoT DPS の IP 接続フィルター
description: Azure IoT DPS インスタンスへの特定の IP アドレスからの接続をブロックする IP フィルター処理を使用する方法。
author: anastasia-ms
ms.author: v-stharr
ms.service: iot-dps
services: iot-dps
ms.topic: how-to
ms.date: 11/12/2021
ms.openlocfilehash: 9354b1e3bfc57951cff919b0c14e3bc950939d8f
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485310"
---
# <a name="use-azure-iot-dps-ip-connection-filters"></a>Azure IoT DPS の IP 接続フィルターを使用する

セキュリティは IoT ソリューションの重要な 1 つの側面です。 場合によっては、セキュリティ構成の一部として、デバイスが接続できる IP アドレスを明示的に指定する必要があります。 Azure IoT Hub Device Provisioning Service (DPS) の *IP フィルター* 機能を使用すると、特定の IPv4 アドレスからのトラフィックを拒否または許可するための規則を構成できます。

## <a name="when-to-use"></a>使用する場合

特定の IP アドレスから DPS エンドポイントへの接続をブロックすると有用な特定のユース ケースには、次の 2 つがあります。

* DPS が指定された範囲の IP アドレスからのトラフィックのみを受信し、それ以外のトラフィックをすべて拒否する必要がある場合。 たとえば、DPS を [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) と共に使用して、DPS と使用しているデバイスとの間にプライベート接続を作成する場合が該当します。

* DPS の管理者によって疑わしいと識別された IP アドレスからのトラフィックを拒否する必要がある場合。

>[!Note]
>IP フィルタリングが有効になっている場合は、Azure portal を使用してサービス操作を実行することができなくなります (登録の管理など)。 ポータルを使用してサービス操作を実行するには、IP フィルタリングを一時的に非アクティブ化し、作業を完了してから、IP フィルタリング機能を再度有効にする必要があります。 独自のクライアントを使用し、IP フィルターの非アクティブ化を回避するには、マシンの IP アドレスを `ipFilterRules` に追加し、CLI を使用して DPS で登録を管理します。

## <a name="how-filter-rules-are-applied"></a>フィルター規則の適用方法

IP フィルター規則は、DPS インスタンス レベルで適用されます。 したがって、IP フィルター規則は、サポートされているプロトコルを使用するデバイスおよびバックエンド アプリからのすべての接続に適用されます。

DPS インスタンスの拒否 IP 規則に一致する IP アドレスからの接続試行に対しては、認証されていないアクセスを示す 401 状態コードとその説明が返されます。 IP 規則に関する記述は応答メッセージに含まれません。

> [!IMPORTANT]
> IP アドレスを拒否すると、他の Azure サービスが DPS インスタンスと対話できなくなる可能性があります。

## <a name="default-setting"></a>既定の設定

既定では、IP フィルタリングが無効になっており、 **[パブリック ネットワーク アクセス]** は [すべてのネットワーク] に設定されています。 この既定の設定は、DPS が任意の IP アドレスからの接続を受け入れること、つまり、0.0.0.0/0 という IP アドレス範囲を受け入れるルールに準拠することを意味します。

:::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-default.png" alt-text="IoT DPS の既定の IP フィルター設定":::

## <a name="add-an-ip-filter-rule"></a>IP フィルター規則の追加

IP フィルター規則を追加するには:

1. [Azure ポータル](https://portal.azure.com)にアクセスします。

2. 左側のメニューまたはポータル ページで、 **[すべてのリソース]** を選択します。

3. ご利用の Device Provisioning Service を選択します。

4. 左側の **[設定]** メニューで *[ネットワーク]* を選択します。

5. **[パブリック ネットワーク アクセス]** の [Selected IP ranges]\(選択された IP 範囲\) を選択します。

6. **[+ IP フィルター規則の追加]** を選択します。

    :::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-add-rule.png" alt-text="IoT DPS に IP フィルター規則を追加する。":::

7. 以下のフィールドを設定します。

    | フィールド | 説明|
    |-------|------------|
    | **名前** |長さが最大 128 文字で、大文字と小文字を区別しない一意の英数字文字列。 ASCII 7 ビット英数字と `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` のみを使用できます。|
    | **アドレス範囲** |1 つの IPv4 アドレスか、または CIDR 表記法で記述した IP アドレス ブロック。 たとえば、CIDR 表記 192.168.100.0/22 は、192.168.100.0 ～ 192.168.103.255 までの 1,024 個の IPv4 アドレスを表します。|
    | **操作** |**[許可]** または **[拒否]** を選択します。|

    :::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png" alt-text="[IP フィルター規則の追加] を選択した後。":::

8. **[保存]** を選択します。 更新が進行中であることを通知するアラートが表示されます。

    :::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png" alt-text="IP フィルター規則の保存に関する通知。":::

    >[!Note]
    > IP フィルター規則が最大値の 100 個に達すると、 **[+ IP フィルター規則の追加]** は無効になります。

## <a name="edit-an-ip-filter-rule"></a>IP フィルター規則の編集

既存のルールを編集するには:

1. 変更する IP フィルター規則データを選択します。

    :::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-rule-edit.png" alt-text="IP フィルター規則を編集する。":::

2. 変更を行います。

3. **[保存]** を選択します。

## <a name="delete-an-ip-filter-rule"></a>IP フィルター規則の削除

IP フィルター規則を削除するには:

1. 削除する IP 規則の行にある削除アイコンを選択します。

    :::image type="content" source="./media/iot-dps-ip-filtering/ip-filter-delete-rule.png" alt-text="IoT DPS IP フィルター規則を削除する。":::

2. **[保存]** を選択します。

## <a name="ip-filter-rule-evaluation"></a>IP フィルター規則の評価

IP フィルター規則は順番に適用されます。 IP アドレスと最初に一致した規則によって、許可または拒否のアクションが決まります。

たとえば、範囲 192.168.100.0/22 内のアドレスを許可し、それ以外のアドレスをすべて拒否するには、グリッドの最初の規則でアドレス範囲 192.168.100.0/22 を許可する必要があります。 さらに、次の規則で範囲 0.0.0.0/0 を使用してすべてのアドレスを拒否する必要があります。

IP フィルター規則の順序を変更するには:

1. 移動する規則を選択します。

2. 目的の場所に規則をドラッグ アンド ドロップします。

3. **[保存]** を選択します。

## <a name="update-ip-filter-rules-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用した IP フィルター規則の更新

DPS IP フィルターは次の 2 とおりの方法で更新できます。

1. IoT Hub Resource REST API メソッドを呼び出します。 REST を使用して IP フィルター規則を更新する方法については、[「Iot Hub リソース - Update」メソッド](/api/iothub/iot-hub-resource/update)の[「定義」セクション](/api/iothub/iot-hub-resource/update#definitions)の「`IpFilterRule`」を参照してください。

2. Azure Resource Manager テンプレートを使用します。 Resource Manager テンプレートの使い方のガイダンスについては、[Azure Resource Manager テンプレート](../azure-resource-manager/templates/overview.md)に関するページを参照してください。 Azure Resource Manager テンプレートを使用して DPS IP フィルター規則を作成、編集、削除する方法については、以降の例で紹介します。

    >[!NOTE]
    >Azure CLI と Azure PowerShell は現在、DPS IP フィルター規則の更新をサポートしていません。

### <a name="add-an-ip-filter-rule"></a>IP フィルター規則の追加

次のテンプレート例では、すべてのトラフィックを受け入れる "AllowAll" という名前の新しい IP フィルター規則を作成します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

要件に基づいて、テンプレートの IP フィルター規則の属性を更新します。

| 属性                | 説明 |
| ------------------------ | ----------- |
| **FilterName**           | IP フィルター規則の名前を指定します。 これは、一意であり、長さが最大 128 文字の、大文字と小文字を区別しない英数字の文字列である必要があります。 ASCII 7 ビット英数字と  `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`  のみを使用できます。 |
| **操作**               | IP フィルター規則のアクションとして許容される値は、**Accept**  または  **Reject**  です。 |
| **ipMask**               | 1 つの IPv4 アドレスか、または CIDR 表記法で記述した IP アドレス ブロックを指定します。 たとえば、CIDR 表記 192.168.100.0/22 は、192.168.100.0 ～ 192.168.103.255 までの 1,024 個の IPv4 アドレスを表します。 |


### <a name="update-an-ip-filter-rule"></a>IP フィルター規則の更新

次のテンプレート例では、前に示した "AllowAll" という名前の IP フィルター規則を更新して、すべてのトラフィックを拒否します。

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>IP フィルター規則の削除

次のテンプレート例では、DPS インスタンスのすべての IP フィルター規則を削除します。

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```

## <a name="next-steps"></a>次のステップ

DPS の管理の詳細については、以下を参照してください。

* [IoT DPS IP アドレスについて](iot-dps-understand-ip-address.md)
* [Azure CLI を使用して DPS を設定する](quick-setup-auto-provision-cli.md)
* [DPS へのアクセスを制御する](how-to-control-access.md)

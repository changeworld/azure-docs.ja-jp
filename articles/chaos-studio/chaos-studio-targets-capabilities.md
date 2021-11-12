---
title: Azure Chaos Studio でのターゲットと機能
description: ターゲットと機能を使用して、Azure Chaos Studio でリソースのオンボードを制御する方法について説明します。
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 74e3db42471fb4322a9c80cd63f30fee730d7a49
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371488"
---
# <a name="targets-and-capabilities-in-azure-chaos-studio"></a>Azure Chaos Studio でのターゲットと機能

Azure リソースに対してエラーを注入する前に、リソースに対応するターゲットと機能が有効になっている必要があります。 ターゲットと機能は、フォールト インジェクションに対して有効にするリソースと、それらのリソースに対して実行できるエラーを制御します。 Azure Chaos Studio では、ターゲットと機能を[他のセキュリティ対策と共に](chaos-studio-permissions-security.md)使用することで、偶発的な事故や悪意のあるフォールト インジェクションを避けることができます。 たとえば、ターゲットと機能を使用すると、CPU 負荷障害が稼働中の仮想マシンに反して実行されるのを許可しながら、それらに対する強制終了プロセス エラーを防ぐことができます。

## <a name="targets"></a>対象サーバー

カオスの **ターゲット化** により、Azure Chaos Studio が特定のターゲットの種類のリソースを操作できるようになります。 **ターゲットの種類** は、リソースに対してエラーを注入する方法を表します。 サービスダイレクト障害のみをサポートするリソースの種類は、Azure Cosmos DB の `Microsoft-CosmosDB` の種類など、1 つのターゲットの種類を持ちます。 サービスダイレクトおよびエージェントベースのエラーをサポートするリソースの種類には、サービスダイレクト フォールト (`Microsoft-VirtualMachine` など) 用とエージェントベースの障害用 (常に `Microsoft-Agent`) 用の 2 種類のターゲットがあります。

ターゲットは、Azure Chaos Studio にオンボードされるリソース (仮想マシンまたはネットワーク セキュリティ グループなど) の子として作成される拡張リソースです。 ターゲットは、リソースで有効になっているターゲットの種類を定義します。 たとえば、このリソース ID を使用して Cosmos DB インスタンスをオンボードする場合は、次のようになります。

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/chaosstudiodemo/providers/Microsoft.DocumentDB/databaseAccounts/myDB
```

Cosmos DB リソースには、次のように書式設定された子リソースがあります。

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/chaosstudiodemo/providers/Microsoft.DocumentDB/databaseAccounts/myDB/providers/Microsoft.Chaos/targets/Microsoft-CosmosDB
```

ターゲットの作成元にされたリソースのみが、Chaos Studio を使用したフォールト インジェクションの対象にできます。

## <a name="capabilities"></a>機能

**機能** によって、Azure Chaos Studio がリソースに対して特定のフォールトを実行できるようになります。たとえば、仮想マシンをシャットダウンすることができます。 機能は、ターゲットの種類ごとに一意であり、で有効になっているエラーを表します (`CPUPressure-1.0` など)。 [Azure Chaos Studio のフォールト ライブラリ](chaos-studio-fault-library.md)にアクセスして、使用可能なすべてのエラーとそれに対応する機能の名前とターゲットの種類を把握します。

機能とは、ターゲットの子として作成された拡張リソースのことです。 たとえば、サービスダイレクト ターゲット ID を持つ仮想マシンでシャットダウン エラーを有効にする場合は、次のようになります。

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine
```

ターゲット リソースには、次のように書式設定された子リソースがあります。

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0
```

実験では、対応する機能が有効になっているオンボード ターゲットに対してのみ、エラーを注入できます。 

## <a name="listing-capability-names-and-parameters"></a>機能の名前とパラメーターの一覧表示
参考までに、機能名、フォールト URN、パラメーターの一覧は[フォールト ライブラリ](chaos-studio-fault-library.md)で利用できますが、HTTP 応答を使用して機能を作成でき、この情報は既存の機能に対して GET を実行することでオン デマンドで取得できます。 たとえば、VM のシャットダウン機能に対して GET を実行すると、次のようになります。

```bash
az rest --method get --url "https://management.azure.com/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0?api-version=2021-08-11-preview"
```

次の JSON が返されます。

```JSON
{
  "id": "/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0",
  "name": "shutdown-1.0",
  "properties": {
    "description": null,
    "name": "shutdown-1.0",
    "parametersSchema": "https://schema-tc.eastus.chaos-prod.azure.com/targetTypes/Microsoft-VirtualMachine/capabilityTypes/Shutdown-1.0/parametersSchema.json",
    "publisher": "Microsoft",
    "targetType": "VirtualMachine",
    "type": "shutdown",
    "urn": "urn:csci:microsoft:virtualMachine:shutdown/1.0",
    "version": "1.0"
  },
  "resourceGroup": "myRG",
  "systemData": {
    "createdAt": "2021-09-15T23:00:00.826575+00:00",
    "lastModifiedAt": "2021-09-15T23:00:00.826575+00:00"
  },
  "type": "Microsoft.Chaos/targets/capabilities"
}
```

`properties.urn` プロパティは、カオス実験で実行するエラーを定義するために使用されます。 このエラーのパラメーターのスキーマを理解するために、`properties.parametersSchema` で参照されるスキーマを GET によって取得できます。

```bash
az rest --method get --url "https://schema-tc.eastus.chaos-prod.azure.com/targetTypes/Microsoft-VirtualMachine/capabilityTypes/Shutdown-1.0/parametersSchema.json"
```

次の JSON を返します。
```JSON
{
  "$schema": "https://json-schema.org/draft-07/schema",
  "properties": {
    "abruptShutdown": {
      "type": "boolean"
    },
    "restartWhenComplete": {
      "type": "boolean"
    }
  },
  "type": "object"
}
```

## <a name="next-steps"></a>次のステップ
ターゲットと機能について理解したので、次の理解に進んでください。
- [エラーとアクションについて](chaos-studio-faults-actions.md)
- [最初の実験を作成して実行する](chaos-studio-tutorial-service-direct-portal.md)

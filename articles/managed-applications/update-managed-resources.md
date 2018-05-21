---
title: Azure マネージ アプリケーションのリソースを更新する | Microsoft Docs
description: Azure マネージ アプリケーションのマネージ リソース グループを操作する方法について説明します。
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 7c2b38055771dae458e4a3a56c2c98231335ae03
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/18/2018
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Azure マネージ アプリケーションのマネージ リソース グループを操作する

この記事では、マネージ アプリケーションの一部としてデプロイされているリソースを更新する方法について説明します。 マネージ アプリケーションの発行元として、マネージ リソース グループ内のリソースにアクセスできます。 これらのリソースを更新するには、マネージ アプリケーションに関連付けられているマネージ リソース グループを見つけて、そのリソース グループ内のリソースにアクセスする必要があります。

この記事では、マネージ アプリケーションを「[Managed Web Application (IaaS) with Azure management services](https://github.com/Azure/azure-managedapp-samples/tree/master/samples/201-managed-web-app)」(Azure マネージ サービスを使用するマネージ Web アプリケーション (IaaS)) サンプル プロジェクトにデプロイしていることを前提としています。 そのマネージ アプリケーションには、**Standard_D1_v2** 仮想マシンが含まれています。 マネージ アプリケーションをデプロイしていない場合でも、この記事で、マネージ リソース グループを更新するための手順に慣れることができます。

次の図は、デプロイ済みのマネージ アプリケーションを示しています。

![デプロイ済みのマネージ アプリケーション](./media/update-managed-resources/deployed.png)

この記事では、Azure CLI を使用して以下の操作を行います。

* マネージ アプリケーションを識別する
* マネージ リソース グループを識別する
* マネージ リソース グループ内の仮想マシン リソースを識別する
* VM のサイズを変更する (利用されていなければ小さなサイズに、高い負荷をサポートする場合は大きなサイズに変更します)
* 許可される場所を指定するポリシーをマネージ リソース グループに割り当てる

## <a name="get-managed-application-and-managed-resource-group"></a>マネージ アプリケーションとマネージ リソース グループを取得する

リソース グループ内のマネージ アプリケーションを取得するには、次のコマンドを使用します。

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

マネージ リソース グループの ID を取得するには、次のコマンドを使用します。

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>マネージ リソース グループ内の VM のサイズを変更する

マネージ リソース グループ内の仮想マシンを表示するには、マネージ リソース グループの名前を指定します。

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

VM のサイズを更新するには、次のコマンドを使用します。

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

操作が完了したら、アプリケーションが Standard D2 v2 で実行されていることを確認します。

![Standard D2 v2 を使用するマネージ アプリケーション](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>マネージ リソース グループにポリシーを適用する

マネージ リソース グループを取得し、そのスコープのポリシーを割り当てます。 ポリシー **e56962a6-4747-49cd-b67b-bf8b01975c4c** は、許可される場所を指定するための組み込みポリシーです。

```azurecli-interactive
managedGroup=$(az managedapp show --name <app-name> --resource-group DemoApp --query managedResourceGroupId --output tsv)

az policy assignment create --name locationAssignment --policy e56962a6-4747-49cd-b67b-bf8b01975c4c --scope $managedGroup --params '{
                            "listofallowedLocations": {
                                "value": [
                                    "northeurope",
                                    "westeurope"
                                ]
                            }
                        }'
```

許可される場所を表示するには、次のコマンドを使用します。

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

ポリシーの割り当てがポータルに表示されます。

![ポリシーの割り当ての表示](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>次の手順

* マネージ アプリケーションの概要については、[マネージ アプリケーションの概要](overview.md)に関するページをご覧ください。
* サンプル プロジェクトについては、[Azure マネージ アプリケーションのサンプル プロジェクト](sample-projects.md)に関する記事を参照してください。

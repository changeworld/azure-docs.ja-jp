---
title: Azure PowerShell の例 - Azure クラウド サービス (延長サポート) をリセットする
description: Azure クラウド サービス (延長サポート) のデプロイをリセットするサンプル スクリプト
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 40b44fd277eac14a5bf2c15f58fccfd9d5b156c4
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881487"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Azure クラウド サービス (延長サポート) をリセットする 
ここに挙げたサンプルでは、Azure クラウド サービス (延長サポート) の既存のデプロイをリセットする際のさまざまな方法を紹介します。

## <a name="reimage-role-instances-of-cloud-service"></a>クラウド サービスのロール インスタンスを再イメージ化する
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances -Reimage
```
このコマンドは、ContosOrg というリソース グループに所属する ContosoCS というクラウド サービスの 2 つのロール インスタンス (**ContosoFrontEnd\_IN\_0** と **ContosoBackEnd\_IN\_1**) を再イメージ化するものです。

## <a name="reimage-all-roles-of-cloud-service"></a>クラウド サービスのロールをすべて再イメージ化する
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Reimage
```

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>クラウド サービスの単一のロール インスタンスを再イメージ化する
```powershell
Reset-AzCloudServiceRoleInstance -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0" -Reimage
```

## <a name="restart-a-single-role-instance-of-a-cloud-service"></a>クラウド サービスの単一のロール インスタンスを再起動する
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Restart
```

## <a name="next-steps"></a>次のステップ

- Azure Cloud Services (延長サポート) の詳細については、[Azure Cloud Services (延長サポート) の概要](overview.md)に関するページを参照してください。
- [Cloud Services (延長サポート) のサンプル リポジトリ](https://github.com/Azure-Samples/cloud-services-extended-support)を確認します。
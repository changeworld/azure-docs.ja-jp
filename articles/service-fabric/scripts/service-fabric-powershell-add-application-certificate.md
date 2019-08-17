---
title: Azure PowerShell のサンプル スクリプト - アプリケーション証明書のクラスターへの追加 | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - アプリケーション証明書の Service Fabric クラスターへの追加。
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 89094dc959f3a258370afc3cfb720aa3b101d1b7
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036137"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>アプリケーション証明書の Service Fabric クラスターへの追加

このサンプル スクリプトは、指定された Azure Key Vault 内に自己署名証明書を作成し、Service Fabric クラスターのすべてのノードにこの証明書をインストールします。 証明書はローカル フォルダーにもダウンロードされます。 ダウンロードされる証明書の名前は、キー コンテナー内の証明書の名前と同じです。 必要に応じてパラメーターをカスタマイズします。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/overview)の手順に従って Azure PowerShell をインストールし、`Connect-AzAccount` を実行して、Azure との接続を作成します。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-application-certificate/add-new-application-certificate.ps1 "Add an application certificate to a cluster")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [Add-AzServiceFabricApplicationCertificate](/powershell/module/az.servicefabric/Add-azServiceFabricApplicationCertificate) | クラスターを構成する仮想マシン スケール セットに新しいアプリケーション証明書を追加します。  |

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の Azure Service Fabric 用 Azure PowerShell サンプルは、[Azure PowerShell サンプル](../service-fabric-powershell-samples.md)のページにあります。

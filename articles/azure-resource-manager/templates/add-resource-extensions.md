---
title: 拡張機能を使用したデプロイ後の構成
description: Azure Resource Manager テンプレート拡張子を使用して、デプロイ後の構成を指定する方法について説明します。
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: b3c4110c8761b3e8daf324d65ac7fa1dcbcdf61f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023499"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>拡張機能を使用してデプロイ後の構成を指定する

テンプレート拡張機能は、Azure リソースでのデプロイ後の構成と自動タスクを提供する小さなアプリケーションです。 最も人気のあるテンプレートは、仮想マシンの拡張機能に関するものです。 「[仮想マシンの拡張機能とその機能について (Windows)](../../virtual-machines/extensions/features-windows.md)」と「[仮想マシンの拡張機能とその機能について (Linux)](../../virtual-machines/extensions/features-linux.md)」をご覧ください。

## <a name="extensions"></a>拡張機能

既存の拡張機能:

- [Microsoft.Compute/virtualMachines/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft.HDInsight clusters/extensions](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Microsoft.Sql servers/databases/extensions](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/siteextensions](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

使用可能な拡張機能を確認するには、[テンプレート リファレンス](https://docs.microsoft.com/azure/templates/)を参照します。 **[タイトルでフィルター処理します]** で、**拡張機能**を入力します。

これらの拡張機能の使用方法については、次をご覧ください。

- [チュートリアル:Azure Resource Manager テンプレートを使用して仮想マシン拡張機能をデプロイする](template-tutorial-deploy-vm-extensions.md)
- [チュートリアル:Azure Resource Manager テンプレートを使用して SQL BACPAC ファイルをインポートする](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:Azure Resource Manager テンプレートを使用して仮想マシン拡張機能をデプロイする](template-tutorial-deploy-vm-extensions.md)

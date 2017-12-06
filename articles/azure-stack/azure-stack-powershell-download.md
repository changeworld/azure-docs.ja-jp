---
title: "GitHub からの Azure Stack ツールのダウンロード | Microsoft Docs"
description: "Azure Stack の操作に必要なツールをダウンロードする方法について説明します。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: 661f24e1271b3f16fddc6426c94464dd8b388f18
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2017
---
# <a name="download-azure-stack-tools-from-github"></a>GitHub からの Azure Stack ツールのダウンロード

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

**AzureStack-Tools** は PowerShell モジュールをホストする GitHub リポジトリで、リソースの管理と Azure Stack へのデプロイに使用できます。 VPN 接続を確立しようとしている場合は、これらの PowerShell モジュールを Azure Stack Development Kit にダウンロードするか、Windows ベースの外部クライアントにダウンロードできます。 これらのツールを入手するには、GitHub リポジトリを複製するか、次のスクリプトを実行して **AzureStack-Tools** フォルダーをダウンロードします。

```PowerShell
# Change directory to the root directory. 
cd \

# Download the tools archive.
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```

## <a name="functionality-provided-by-the-modules"></a>モジュールで提供される機能

**AzureStack-Tools** リポジトリには、Azure Stack の次の機能をサポートする PowerShell モジュールが含まれています。  

| 機能 | 説明 | このモジュールを使用できるユーザー |
| --- | --- | --- |
| [クラウド機能](user/azure-stack-validate-templates.md) | このモジュールを使用すると、クラウドのクラウド機能を取得できます。 たとえば、このモジュールを使用して、API バージョンや Azure Resource Manager リソースなどのクラウド機能を取得できます。 このモジュールを使用して Azure Stack と Azure クラウドの VM 拡張機能を入手することもできます。 | クラウド オペレーターとユーザー |
| [Azure Stack コンピューティングの管理](azure-stack-add-vm-image.md) | このモジュールを使用すると、Azure Stack マーケットプレースに VM イメージを追加したり削除したりできます。 | クラウド オペレーター |
| [Azure Stack インフラストラクチャの管理](https://github.com/Azure/AzureStack-Tools/blob/master/Infrastructure/README.md) | このモジュールを使用すると、Azure Stack インフラストラクチャの VM、アラート、更新などを管理できます。 |  クラウド オペレーター|
| [Azure Stack の Resource Manager ポリシー](user/azure-stack-policy-module.md) | このモジュールを使用すると、Azure Stack と同じバージョン管理とサービス可用性で、Azure サブスクリプションまたは Azure リソース グループを構成できます。 | クラウド オペレーターとユーザー |
| [Azure への登録](azure-stack-register.md) | このモジュールを使用すると、開発キットのインスタンスを Azure に登録できます。 登録後、Azure からマーケットプレースの項目をダウンロードして Azure Stack で使用できます。 | クラウド オペレーター |
| [Azure Stack のデプロイ](azure-stack-run-powershell-script.md) | このモジュールを使用すると、Azure Stack の仮想ハード ディスク (VHD) イメージを使用して、Azure Stack のホスト コンピューターでデプロイおよび再デプロイを行う準備ができます。 | クラウド オペレーター|
| [Azure Stack への接続](azure-stack-connect-powershell.md) | このモジュールを使用すると、PowerShell で Azure Stack インスタンスに接続し、Azure Stack への VPN 接続を構成できます。 | クラウド オペレーターとユーザー |
| [Azure Stack サービスの管理](azure-stack-create-offer.md) | このモジュールを使用すると、コンピューティング、Azure Storage、ネットワーク、および Key Vault のサービス全体でのクォータ無制限の既定のテナント プランを作成できます。   | クラウド オペレーター|
| [テンプレートの検証](user/azure-stack-validate-templates.md) | このモジュールを使用すると、既存のまたは新しいテンプレートを Azure Stack にデプロイできるかどうか確認できます。 | クラウド オペレーターとユーザー|


## <a name="next-steps"></a>次のステップ
* [Azure Stack ユーザーの PowerShell 環境の構成](user/azure-stack-powershell-configure-user.md)   
* [VPN 経由での Azure Stack Development Kit への接続](azure-stack-connect-azure-stack.md)  

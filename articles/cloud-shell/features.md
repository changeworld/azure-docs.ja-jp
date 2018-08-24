---
title: Azure Cloud Shell の機能 | Microsoft Docs
description: Azure Cloud Shell での Bash の機能の概要
services: Azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2018
ms.author: juluk
ms.openlocfilehash: 1321645d97e7f6ff2faed1e61ddb608afcb7b413
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038357"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Azure Cloud Shell の機能とツール

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell は `Ubuntu 16.04 LTS` 上で実行されます。

## <a name="features"></a>機能

### <a name="secure-automatic-authentication"></a>セキュリティで保護された自動認証

Cloud Shell は、Azure CLI 2.0 と Azure PowerShell のアカウント アクセスを安全かつ自動的に認証します。

### <a name="home-persistence-across-sessions"></a>セッション間での $Home の永続化

セッション間でファイルを保持する場合、Cloud Shell の初回起動時に、Azure ファイル共有のアタッチについてのチュートリアルがあります。
完了すると、今後すべてのセッションで、ストレージが自動的にアタッチされます (`$Home\clouddrive` としてマウントされます) 。
さらに、`$Home` ディレクトリが .img として Azure ファイル共有に永続化されます。
`$Home` およびマシン状態の外部にあるファイルは、セッション間で保持されません。 SSH キーなどのシークレットを格納するときは、ベスト プラクティスを使用します。 [Azure Key Vault などのサービスには、設定用のチュートリアルが用意されています](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites)。

[Cloud Shell でのファイルの永続化については、こちらを参照してください。](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Azure ドライブ (Azure:)

Cloud Shell (プレビュー) の PowerShell は、Azure ドライブ (`Azure:`) で開始します。
Azure ドライブを使用すると、ファイル システムのナビゲーションと同じように、Compute、Network、Storage などの Azure リソースを簡単に検出およびナビゲーションできるようになります。
使用しているドライブに関係なく、引き続き使い慣れた [Azure PowerShell コマンドレット](https://docs.microsoft.com/powershell/azure)を使用してこれらのリソースを管理できます。
Azure リソースに対するすべての変更は、Azure Portal で直接行われたものも、Azure PowerShell コマンドレット経由で行われたものも、Azure ドライブに反映されます。  `dir -Force` を実行してリソースを最新の情報に更新できます。

![](media/features-powershell/azure-drive.png)

### <a name="deep-integration-with-open-source-tooling"></a>オープンソース ツールとの緊密な統合

Cloud Shell には、Terraform、Ansible、Chef InSpec などのオープンソース ツールのための事前に構成された認証が含まれています。 チュートリアルの例からそれを試してみてください。

## <a name="tools"></a>ツール

|Category   |Name   |
|---|---|
|Linux ツール            |Bash<br> zsh<br> sh<br> tmux<br> dig<br>               |
|Azure ツール            |[Azure CLI 2.0](https://github.com/Azure/azure-cli) と [1.0](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) |
|テキスト エディター           |vim<br> nano<br> emacs       |
|ソース管理         |git                    |
|ビルド ツール            |make<br> maven<br> npm<br> pip         |
|Containers             |[Docker CLI](https://github.com/docker/cli)/[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|データベース              |MySQL クライアント<br> PostgreSql クライアント<br> [sqlcmd ユーティリティ](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|その他                  |iPython クライアント<br> [Cloud Foundry CLI](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)| 

## <a name="language-support"></a>言語のサポート

|Language   |Version   |
|---|---|
|.NET Core  |2.0.0       |
|Go         |1.9        |
|Java       |1.8        |
|Node.js    |8.9.4      |
|PowerShell |[6.1.0-preview.4](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 および 3.5 (既定)|

## <a name="next-steps"></a>次の手順
[Cloud Shell の Bash のクイックスタート](quickstart.md) <br>
[Cloud Shell での PowerShell (プレビュー) のクイックスタート](quickstart-powershell.md) <br>
[Azure CLI 2.0 について](https://docs.microsoft.com/cli/azure/) <br>
[Azure PowerShell の概要](https://docs.microsoft.com/powershell/azure/) <br>

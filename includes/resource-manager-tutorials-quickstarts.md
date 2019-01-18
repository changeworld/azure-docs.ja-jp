---
title: インクルード ファイル
description: インクルード ファイル
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/05/2018
ms.author: jgao
ms.custom: include file
ms.openlocfilehash: 711688b5b414e7b9a931190de3f7e6321d97ba3e
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53729341"
---
## <a name="quickstarts-and-tutorials"></a>クイック スタートとチュートリアル

次のクイック スタートとチュートリアルを使用して、Resource Manager テンプレートの開発方法を学ぶことができます。

- クイック スタート

    |タイトル|説明|
    |------|-----|
    |[Azure Portal の使用](../articles/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)|ポータルを使用してテンプレートを生成します。また、テンプレートを編集してデプロイするプロセスについて説明しています。|
    |[Visual Studio Code を使用する](../articles/azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code.md)|Visual Studio Code を使用してテンプレートを作成および編集します。また、Azure Cloud Shell を使用してテンプレートをデプロイする方法を説明しています。|
    |[Visual Studio を使用する](../articles/azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)|Visual Studio を使用してテンプレートを作成、編集、デプロイします。|

- チュートリアル

    |タイトル|説明|
    |------|-----|
    |[テンプレート リファレンスの利用](../articles/azure-resource-manager/resource-manager-tutorial-create-encrypted-storage-accounts.md)|テンプレートを開発するためにテンプレート リファレンス ドキュメントを利用します。 このチュートリアルでは、ストレージ アカウントのスキーマを確認し、その情報を使用して、暗号化されたストレージ アカウントを作成します。|
    |[複数のインスタンスの作成](../articles/azure-resource-manager/resource-manager-tutorial-create-multiple-instances.md)|Azure リソースの複数のインスタンスを作成します。 このチュートリアルでは、ストレージ アカウントの複数のインスタンスを作成します。|
    |[リソースの移動](../articles/azure-resource-manager/resource-manager-tutorial-move-resources.md)|リソースを 1 つのリソース グループから別のリソース グループに移動します。 このチュートリアルでは、既存のテンプレートを実行して 2 つのリソース グループと 1 つのストレージ アカウントを作成した後、Azure PowerShell コマンドレットを実行してそのストレージ アカウントを他のリソース グループに移動します。|
    |[リソースのデプロイ順序の設定](../articles/azure-resource-manager/resource-manager-tutorial-create-templates-with-dependent-resources.md)|リソースの依存関係を定義します。 このチュートリアルでは、仮想ネットワーク、仮想マシン、および依存する Azure リソースを作成します。 依存関係を定義する方法を説明しています。|
    |[使用条件](../articles/azure-resource-manager/resource-manager-tutorial-use-conditions.md)|いくつかのパラメーター値に基づいてリソースをデプロイします。 このチュートリアルでは、新しいストレージ アカウントを作成するか、パラメーターの値に基づいて既存のストレージ アカウントを使用するためのテンプレートを定義します。|
    |[キー コンテナーの統合](../articles/azure-resource-manager/resource-manager-tutorial-use-key-vault.md)|Azure Key Vault からシークレット/パスワードを取得します。 このチュートリアルでは、仮想マシンを作成します。  仮想マシンの管理者のパスワードは、キー コンテナーから取得されます。|
    |[リンク済みテンプレートを作成する](../articles/azure-resource-manager/resource-manager-tutorial-create-linked-templates.md)|テンプレートをモジュール化し、テンプレートから他のテンプレートを呼び出します。 このチュートリアルでは、仮想ネットワーク、仮想マシン、および依存するリソースを作成します。  依存するストレージ アカウントは、リンクされたテンプレートで定義されます。 |
    |[仮想マシン拡張機能のデプロイ](../articles/azure-resource-manager/resource-manager-tutorial-deploy-vm-extensions.md)|拡張機能を使用して、デプロイ後タスクを実行します。 このチュートリアルでは、仮想マシンに Web サーバーをインストールするために、カスタム スクリプト拡張機能をデプロイします。 |
    |[SQL 拡張機能のデプロイ](../articles/azure-resource-manager/resource-manager-tutorial-deploy-sql-extensions-bacpac.md)|拡張機能を使用して、デプロイ後タスクを実行します。 このチュートリアルでは、仮想マシンに Web サーバーをインストールするために、カスタム スクリプト拡張機能をデプロイします。 |
    |[成果物のセキュリティ保護](../articles/azure-resource-manager/resource-manager-tutorial-secure-artifacts.md)|デプロイを完了するために必要な成果物をセキュリティで保護します。 このチュートリアルでは、SQL 拡張機能のデプロイに関するチュートリアルで使用した成果物をセキュリティで保護する方法について説明します。 |
    |[安全なデプロイ プラクティスの使用](../articles/azure-resource-manager/deployment-manager-tutorial.md)|Azure Deployment Manager を使用します。 |

これらのチュートリアルは、Resource Manager テンプレート開発の主要な概念について、個別にまたはシリーズとして使用できます。
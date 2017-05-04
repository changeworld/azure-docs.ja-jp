---
title: "Azure Resource Manager テンプレートと PowerShell を使ってラボを自動的に作成または変更する | Microsoft Docs"
description: "Azure Resource Manager テンプレートと PowerShell を使用して DevTest ラボでラボを自動的に作成または変更する方法を学習します"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: dad9944c-0b20-48be-ba80-8f4aa0950903
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 8ee0301a48265b91f951c96c3339a3c3e3bdb1cb
ms.lasthandoff: 04/27/2017


---

# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Azure Resource Manager テンプレートと PowerShell を使ってラボを自動的に作成または変更する

DevTest ラボが提供する数多くの Azure Resource Manager テンプレートと PowerShell スクリプトは、新規ラボの作成や既存ラボの変更とこれらのリソースのデプロイを速く、自動的に行うのに役立ちます。

この記事では、これらのテンプレートとスクリプトを使用してラボの作成、変更、デプロイを自動化するプロセスを説明します。 この記事では、PowerShell を使用して DevTest ラボでいくつかの一般的タスクを実行する方法の詳細も紹介します。

## <a name="step-1-gather-your-templates-and-scripts"></a>ステップ 1: テンプレートとスクリプトを収集する
あらかじめ定義された [Azure Resource Manager テンプレート](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)と [PowerShell スクリプト](https://github.com/Azure/azure-devtestlab/tree/master/Scripts)が、当社が公開する [Github リポジトリ](https://github.com/Azure/azure-devtestlab)にあります。 これらをそのまま使用するか、ニーズに合わせてカスタマイズして、独自の[プライベート Git リポジトリ](devtest-lab-add-artifact-repo.md)に保管できます。 

## <a name="step-2-modify-your-azure-resource-manager-template"></a>ステップ 2: Azure Resource Manager テンプレートの変更
[Azure Resource Manager テンプレートを使用してマルチ VM 環境と PaaS リソースを作成する](devtest-lab-create-environment-from-arm.md)では、Azure Resource Manager テンプレートを DevTest ラボ内で使用して Azure ソリューションのインフラストラクチャと構成を定義し、複数の VM を一貫した状態で繰り返しデプロイする方法を説明しています。

たとえば、新しい仮想ネットワークを作成して既存のラボすべてに適用したい場合は、Azure Resource Manager テンプレートを使用すると簡単にできます。

## <a name="step-3-deploy-resources-with-powershell"></a>ステップ 3: PowerShell によりリソースをデプロイする
テンプレートやスクリプトをカスタマイズした後で、[Resource Manager テンプレートと Azure PowerShell を使用してリソースをデプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)するために必要な手順に従ってください。 この記事では、Azure PowerShell と Azure Resource Manager テンプレートを使用してリソースを Azure にデプロイする場合の概要を説明します。


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>DevTest ラボで PowerShell を使用して実行できる一般的なタスク
PowerShell を使用して自動化できる一般的タスクが他にも多数あります。 ドキュメントの以下のセクションで、これらのタスクを実行するために必要な手順を概説します。

* [PowerShell を使用して VHD ファイルからカスタム イメージを作成する](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [PowerShell を使用してラボのストレージ アカウントに VHD ファイルをアップロードする](devtest-lab-upload-vhd-using-powershell.md)
* [PowerShell を使用してラボに外部ユーザーを追加する](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [PowerShell を使用してラボ カスタム ロールを作成する](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>次のステップ
* カスタマイズしたテンプレートまたはスクリプトを保存するための[プライベート Git リポジトリ](devtest-lab-add-artifact-repo.md)を作成する方法を学習します。
* [Azure クイックスタート テンプレート ギャラリーから Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates)を検索します。


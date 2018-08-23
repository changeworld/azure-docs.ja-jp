---
title: Azure Stack で Azure Resource Manager テンプレートを使用する | Microsoft Docs
description: Azure Stack で Azure Resource Manager テンプレートを使用してリソースをプロビジョニングする方法を説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: jeffgo
ms.openlocfilehash: 456f27b97ee644aef34f9bb9e2c0525bd61c1c84
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "41946536"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Azure Stack で Azure リソース マネージャー テンプレートを使用する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Resource Manager テンプレートを使用して、お使いのアプリケーションのすべてのリソースを、単一の連携した操作でデプロイしてプロビジョニングできます。 テンプレートを再デプロイして、リソース グループ内のリソースを変更することもできます。

これらのテンプレートは、Microsoft Azure Stack ポータル、PowerShell、コマンド ライン、および Visual Studio を使用してデプロイできます。

以下のクイックスタート テンプレートを [GitHub](http://aka.ms/azurestackgithub) で入手できます。

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>SharePoint サーバーのデプロイ (非高可用性デプロイ)

PowerShell DSC 拡張機能を使用して、以下のリソースを含む SharePoint Server 2013 ファームを作成します。

* 仮想ネットワーク
* 3 つのストレージ アカウント
* 2 つの外部ロード バランサー
* 単一のドメインを使用する新しいフォレスト用のドメイン コントローラーとして構成された 1 台の仮想マシン (VM)
* SQL Server 2014 のスタンドアロン サーバーとして構成された 1 台の VM
* ワンマシン SharePoint Server 2013 ファームとして構成された 1 台の VM

## <a name="deploy-ad-non-high-availability-deployment"></a>AD のデプロイ (非高可用性デプロイ)

PowerShell DSC 拡張機能を使用して、以下のリソースを含む AD ドメイン コントローラーを作成します。

* 仮想ネットワーク
* 1 つのストレージ アカウント
* 1 つの外部ロード バランサー
* 単一のドメインを使用する新しいフォレスト用のドメイン コントローラーとして構成された 1 台の仮想マシン (VM)

## <a name="deploy-adsql-non-high-availability-deployment"></a>AD/SQL のデプロイ (非高可用性デプロイ)

PowerShell DSC 拡張機能を使用して、以下のリソースを含む SQL Server 2014 スタンドアロン サーバーを作成します。

* 仮想ネットワーク
* 2 つのストレージ アカウント
* 1 つの外部ロード バランサー
* 単一のドメインを使用する新しいフォレスト用のドメイン コントローラーとして構成された 1 台の仮想マシン (VM)
* SQL Server 2014 のスタンドアロン サーバーとして構成された 1 台の VM

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

PowerShell DSC 拡張機能を使用して、既存のバーチャル マシンのローカル構成マネージャー (LCM) を構成し、それを Azure Automation Account DSC Pull Server に登録します。

## <a name="create-a-virtual-machine-from-a-user-image"></a>ユーザー イメージからの仮想マシンの作成

カスタム ユーザー イメージから仮想マシンを作成します。 このテンプレートは、仮想ネットワーク (DNS 付き)、パブリック IP アドレス、およびネットワーク インターフェイスもデプロイします。

## <a name="basic-virtual-machine"></a>基本的な仮想マシン

仮想ネットワーク (DNS 付き)、パブリック IP アドレス、およびネットワーク インターフェイスを含む Windows VMをデプロイします。

## <a name="cancel-a-running-template-deployment"></a>実行中のテンプレートのデプロイのキャンセル

実行中のテンプレートのデプロイをキャンセルするには、`Stop-AzureRmResourceGroupDeployment` PowerShell コマンドレットを使用します。

## <a name="next-steps"></a>次の手順

* [ポータルを使用してテンプレートをデプロイする](azure-stack-deploy-template-portal.md)
* [Azure リソース マネージャーの概要](../../azure-resource-manager/resource-group-overview.md)

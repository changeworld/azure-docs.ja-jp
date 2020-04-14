---
title: インクルード ファイル
description: インクルード ファイル
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 4c50ca2449787d681d8b9571083a92077d95dcd8
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986666"
---
これらのサンプルでは、テナント間の管理シナリオで実行できるさまざまなタスクを示しています。

| **テンプレート** | **説明** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-rg-deployment) | 2 つの異なるリソース グループにストレージ アカウントをデプロイします。|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-mgmt-services) | Azure 管理サービスを作成し、それらをリンクし、追加のソリューションをデプロイします。 エンドツーエンドでデプロイする場合は、**rgWithAzureMgmt.json** テンプレートを使用します。 |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-security-center) | 対象となる Azure サブスクリプション内で Azure Security Center を有効にし、構成します。 |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-sentinel) | 委任されたサブスクリプションの既存の Log Analytics ワークスペースで Azure Sentinel をデプロイして有効にします。 |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-log-analytics-vm-extensions) | これらのテンプレートを使用すると、Windows および Linux の VM に Log Analytics VM 拡張機能をデプロイし、指定した Log Analytics ワークスペースに接続することができます。 |

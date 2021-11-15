---
title: インクルード ファイル
description: インクルード ファイル
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 04/24/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 282fee2bdb9fa6cd063d2c041b57bb1ee5513926
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132354160"
---
これらのサンプルでは、テナント間の管理シナリオで実行できるさまざまなタスクを示しています。

| **テンプレート** | **説明** |
|---------|---------|
| [`create-keyvault-secret`](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret) | 顧客のテナントにキー コンテナーを作成し、アクセス ポリシーを作成します。
| [`cross-rg-deployment`](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-rg-deployment) | 2 つの異なるリソース グループにストレージ アカウントをデプロイします。|
| [`deploy-azure-mgmt-services`](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-mgmt-services) | Azure 管理サービスを作成し、それらをリンクし、追加のソリューションをデプロイします。 エンドツーエンドでデプロイする場合は、**rgWithAzureMgmt.json** テンプレートを使用します。 |
| [`deploy-azure-security-center`](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-security-center) | 対象となる Azure サブスクリプション内で Microsoft Defender for Cloud を有効にし、構成します。 |
| [`deploy-azure-sentinel`](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-sentinel) | 委任されたサブスクリプションの既存の Log Analytics ワークスペースで Microsoft Sentinel をデプロイして有効にします。 |
| [`deploy-log-analytics-vm-extensions`](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-log-analytics-vm-extensions) | これらのテンプレートを使用すると、Windows および Linux の VM に Log Analytics VM 拡張機能をデプロイし、指定した Log Analytics ワークスペースに接続することができます。 |

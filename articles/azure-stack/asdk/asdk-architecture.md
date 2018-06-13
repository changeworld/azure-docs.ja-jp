---
title: Azure Stack Development Kit のアーキテクチャ | Microsoft Docs
description: Azure Stack Development Kit (ASDK) のアーキテクチャについて説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 68da3ac0eb135f5956dfea76e186d9c57beea79c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/17/2018
ms.locfileid: "29974139"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Microsoft Azure Stack Development Kit のアーキテクチャ
Azure Stack Development Kit (ASDK) は、Azure Stack の単一ノード デプロイです。 すべてのコンポーネントは、1 台のホスト マシンで実行されている仮想マシンにインストールされます。 

## <a name="logical-architecture-diagram"></a>論理アーキテクチャ図
次の図は、ASDK とそのコンポーネントの論理アーキテクチャを示しています。

![ASDK アーキテクチャ](media/asdk-architecture/image1.png)

## <a name="virtual-machine-roles"></a>仮想マシンのロール
ASDK は、開発キットのホスト コンピューターにホストされている次の VM を使ってサービスを提供します。

| Name | [説明] |
| ----- | ----- |
| **AzS-ACS01** | Azure Stack ストレージ サービス。|
| **AzS-ADFS01** | Active Directory フェデレーション サービス (ADFS)。  |
| **AzS-BGPNAT01** | エッジ ルーター。Azure Stack 用の NAT 機能と VPN 機能を提供します。 |
| **AzS-CA01** | Azure Stack ロール サービス用の証明機関サービス。|
| **AzS-DC01** | Microsoft Azure Stack 用の Active Directory、DNS、DHCP サービス。|
| **AzS-ERCS01** | 緊急回復コンソールの VM。 |
| **AzS-GWY01** | テナント ネットワーク用の VPN サイト間接続などのエッジ ゲートウェイ サービス。|
| **AzS-NC01** | ネットワーク コントローラー。Azure Stack ネットワーク サービスを管理します。  |
| **AzS-SLB01** | テナントおよび Azure Stack インフラストラクチャ サービス用の、Azure Stack の負荷分散マルチプレクサー サービス。  |
| **AzS-SQL01** | Azure Stack インフラストラクチャ ロール用の内部データ ストア。  |
| **AzS-WAS01** | Azure Stack 管理ポータルと Azure Resource Manager のサービス。|
| **AzS-WASP01**| Azure Stack ユーザー (テナント) ポータルと Azure Resource Manager のサービス。|
| **AzS-XRP01** | Microsoft Azure Stack 用のインフラストラクチャ管理コントローラー。計算、ネットワーク、ストレージのリソースプロバイダーなどがあります。|


## <a name="next-steps"></a>次の手順
[基本的な ASDK 管理タスクについて学習する](asdk-admin-basics.md)

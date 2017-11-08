---
title: "Microsoft Azure Stack Development Kit のリリース ノート | Microsoft Docs"
description: 
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: helaw
ms.openlocfilehash: 5ee2f8164d13e61f9a43abcbda729298f2168518
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2017
---
# <a name="azure-stack-development-kit-release-notes"></a>Azure Stack Development Kit のリリース ノート

*適用対象: Azure Stack 開発キット*

以下のリリース ノートでは、Azure Stack Development Kit の新機能と既知の問題に関する情報を提供します。 実行しているバージョンが不明な場合は、[ポータルを使用して確認](azure-stack-updates.md#determine-the-current-version)できます。

## <a name="release-build-201709283"></a>リリース ビルド 20170928.3

### <a name="known-issues"></a>既知の問題

#### <a name="powershell"></a>PowerShell

- AzureRM 1.2.11 リリースの PowerShell モジュールには、重大な変更が多数追加されています。 1.2.10 バージョンからアップグレードする場合は、[https://aka.ms/azspowershellmigration](https://aka.ms/azspowershellmigration) の移行ガイドを参照してください。


#### <a name="deployment"></a>デプロイ
* デプロイ時に、IP アドレスでタイム サーバーを指定する必要があります。  


#### <a name="portal"></a>ポータル
* ポータルに空のダッシュボードが表示されることがあります。  ポータルの右上にある歯車を選択し、[既定の設定に戻す] を選択することで、ダッシュボードを復元できます。
* テナントはサブスクリプションなしですべてのマーケットプレースを参照することができ、プランやオファーなどの管理アイテムを表示できます。  これらのアイテムはテナントでは機能しません。
* [リソース グループ] の "移動" ボタンは無効になっています。  サブスクリプション間のリソース グループの移動は現在サポートされていないため、このボタンが無効になっていることは正しい動作です。
* Azure Stack ポータルを使用して、サブスクリプションへのアクセス許可を表示することはできません。  回避策として、Powershell を使用してアクセス許可を確認することができます。
* Azure Stack Development Kit を登録するように勧める警告が表示されます。  この警告は正しい動作です。  
  

#### <a name="services"></a>サービス
* 仮想マシン スケール セットを作成するためのマーケットプレース エクスペリエンスはありませんが、テンプレートを使用して作成することはできます。
* ポータルを使用して、パブリック IP アドレスを持つロード バランサーを作成することはできません。  この問題を回避するには、PowerShell を使用してロード バランサーを作成します。
* VM 可用性セットは、1 つの障害ドメインと 1 つの更新ドメインでのみ構成可能です。  
* テナントでは、サブスクリプションで最初の Azure 関数を作成する前に、ストレージ リソースプロバイダーを登録する必要があります。
* テナント サブスクリプションを削除すると、リソースが孤立します。  回避策として、まず、テナント リソースまたはリソース グループ全体を削除してから、テナント サブスクリプションを削除します。 
* ネットワーク ロード バランサーの作成時に、NAT 規則を作成する必要があります。 そうしないと、ロード バランサーを作成した後に NAT 規則を追加しようとするときにエラーが表示されます。
* テナントでは、geo 冗長ストレージを持つ仮想マシンを作成するオプションを利用できます。  この構成の場合、仮想マシンの作成が失敗します。
* テナントでデータベースを新しい SQL または MySQL SKU で作成する際に、最大で 1 時間かかる場合があります。 
* リソース プロバイダーで実行されない SQL および MySQL ホスティング サーバーで直接アイテムを作成することはサポートされていないため、状態が不一致になる場合があります。
* インフラストラクチャ バックアップ ブレードは使用しないでください。


#### <a name="fabric"></a>Fabric
* コンピューティング リソース プロバイダーの状態が不明と表示されます。
* BMC IP アドレスとモデルは、スケール ユニット ノードの重要な情報には表示されません。  Azure Stack Development Kit では、この動作は予期されるものです。


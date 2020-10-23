---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 75aec9f3509881c35de9309fa1532b961fb2bc03
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875563"
---
Azure Portal でクラシック VNets を作成するときに表示される名前は、PowerShell に使用する完全な名前ではありません。 たとえば、Azure Portal に **TestVNet1** という名前で表示されている VNet は、ネットワーク構成ファイルではそれよりもかなり長い名前である可能性があります。 つまり、実際には **Group ClassicRG TestVNet1** のような名前である可能性があります。 接続を作成するときは、ネットワーク構成ファイルに示されている値を使用することが重要です。

次の手順では、Azure アカウントに接続し、ネットワーク構成ファイルをダウンロードして表示し、接続に必要な値を取得します。

1. Azure サービス管理 (SM) PowerShell コマンドレットの最新バージョンをダウンロードしてインストールします。 ほとんどの場合、ローカルに Resource Manager モジュールがインストールされていますが、サービス管理モジュールはありません。 サービス管理モジュールはレガシであるため、個別にインストールする必要があります。 詳細については、[サービス管理用のコマンドレットをインストールする](/powershell/azure/servicemanagement/install-azure-ps)ことに関するページを参照してください。

1. 管理者特権で PowerShell コンソールを開き、アカウントに接続します。 接続するには、次の例を参照してください。 これらのコマンドは、PowerShell サービス管理モジュールを使用してローカルで実行する必要があります。 アカウントに接続します。 接続については、次の例を参考にしてください。

   ```powershell
   Add-AzureAccount
   ```
1. アカウントのサブスクリプションを確認します。

   ```powershell
   Get-AzureSubscription
   ```
1. 複数のサブスクリプションがある場合は、使用するサブスクリプションを選択します。

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```
1. ネットワーク構成ファイルをエクスポートして、表示します。 コンピューターにディレクトリを作成し、ネットワーク構成ファイルをそのディレクトリにエクスポートします。 この例では、ネットワーク構成ファイルは **C:\AzureNet** にエクスポートされます。

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
1. テキスト エディターでファイルを開き、VNet とサイトの名前を確認します。 これらの名前は、接続を作成する際に使用する名前となります。<br>VNet 名は **VirtualNetworkSite name =** と示されています<br>サイト名は **LocalNetworkSiteRef name =** と示されています
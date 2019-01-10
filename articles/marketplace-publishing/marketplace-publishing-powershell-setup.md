---
title: Marketplace 向けの VM を作成するための PowerShell のセットアップ | Microsoft Docs
description: Azure PowerShell をセットアップしてオプションのプロセス フローとして使用し、VM イメージを作成して Azure Marketplace にデプロイし、販売する方法を説明します
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: e19d6cda-76df-4e42-be84-c9fe47a636db
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2016
ms.author: hascipio
ROBOTS: NOINDEX
ms.openlocfilehash: e5175558f18dfc903c280ea6bbe487e0a3ee8189
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54076605"
---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Azure Marketplace のプランを作成するための Azure PowerShell のセットアップ

Azure で PowerShell をセットアップする方法の詳細については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)」をご覧ください。 証明書方式を使用すると、認証に必要な証明書がダウンロードおよびインポートされるため簡単に処理できます。 必要な証明書を取得するには、 **Get-AzurePublishSettingsFile** コマンドレットを使用します。 求められたらファイルを保存します。 証明書を PowerShell セッションにインポートするには、 **Import-AzurePublishSettingsFile** コマンドレットを使用します。

PowerShell セッション用の一般的な Microsoft Azure サブスクリプションの設定を構成して保存するには、**Set-AzureSubscription** コマンドレットと **Select-AzureSubscription** コマンドレットを使用します。

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

最初のコマンドは、既定のストレージ アカウントをサブスクリプションに関連付けます (一部の VM プロビジョニング操作に必要)。  2 番目のコマンドは、そのサブスクリプションを現在のサブスクリプションにします (他のコマンドレットによって認識される)。

詳細については、次の記事を参照してください。
* [概要: Azure Marketplace にプランを発行する](marketplace-publishing-getting-started.md)
* [Marketplace 向けの仮想マシン イメージの作成](marketplace-publishing-vm-image-creation.md)


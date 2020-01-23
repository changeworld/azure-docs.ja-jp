---
title: インクルード ファイル
titleSuffix: Azure
description: インクルード ファイル
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3a5f7157ef8f3645dd03ec93684238dd8bbc067e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75773642"
---
構成を開始する前に、必要なモジュールをインストールしてインポートします。 PowerShell でモジュールをインストールするには、管理者特権が必要です。

1. AD モジュールをインストールしてインポートする
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. AD.Peering モジュールをインストールしてインポートする
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. 次のコマンドを使用して、モジュールが正常にインポートされていることを確認します。
    ```powershell
    Get-Module
    ```
1. 次のコマンドを使用して、Azure アカウントにサインインします。
    ```powershell
    Connect-AzAccount
    ```
1. アカウントのサブスクリプションを確認し、ピアリングを作成するサブスクリプションを選択します。
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. リソース グループがまだない場合は、ピアリングを作成する前に、作成しておく必要があります。 リソース グループを作成するには、次のコマンドを実行します。

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> まだ ASN とサブスクリプションを関連付けていない場合は、[ピア ASN の関連付け](../howto-subscription-association-powershell.md)の手順に従ってください。 これはピアリングを要求するために必要です。

> [!NOTE]
> リソース グループの場所は、ピアリングを設定するために選択する場所とは関係ありません。
[https://github.com/mysqljs/mysql/](&nbsp;)

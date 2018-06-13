---
title: Azure CLI のサンプル - Azure SignalR Service | Microsoft Docs
description: Azure CLI のサンプル - Azure SignalR Service
services: signalr
documentationcenter: signalr
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: signalr
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: signalr
ms.date: 04/20/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 6e76ee89160c84ff0f1033590d14c288f023f201
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
ms.locfileid: "33779856"
---
# <a name="azure-cli-samples"></a>Azure CLI のサンプル

次の表は、Azure CLI を使った Azure SignalR Service 用 Bash スクリプトへのリンクをまとめたものです。

| | |
|-|-|
|**作成**||
| [Create a new SignalR Service and resource group (新しい SignalR Service とリソース グループの作成)](scripts/signalr-cli-create-service.md) | ランダムな名前を使って、新しいリソース グループ内に新しい Azure SignalR Service リソースを作成します。  |
|**統合**||
| [Create a new SignalR Service and Web App configured to use SignalR (新しい SignalR Service と、SignalR を使うよう構成された Web アプリの作成)](scripts/signalr-cli-create-with-app-service.md) | ランダムな名前を使って、新しいリソース グループ内に新しい Azure SignalR Service リソースを作成します。 また、SignalR サービスを使用する ASP.NET Core Web アプリをホストするための、新しい Web アプリと App Service プランを追加します。 この Web アプリは、新しい SignalR サービス リソースに接続するためのアプリ設定を使って構成されます。 |
| [Create a new SignalR Service and Web App configured to use SignalR, and GitHub OAuth (新しい SignalR Service と、SignalR を使うよう構成された Web アプリの作成)](scripts/signalr-cli-create-with-app-service-github-oauth.md) | ランダムな名前を使って、新しいリソース グループ内に新しい Azure SignalR Service リソースを作成します。 また、SignalR サービスを使用する ASP.NET Core Web アプリをホストするための、新しい Azure Web アプリとホスティング プランを追加します。 この Web アプリは、新しい SignalR サービス リソースへの接続文字列と、[GitHub 認証](https://developer.github.com/v3/guides/basics-of-authentication/) ([認証チュートリアル](signalr-authenticate-oauth.md)で説明されています) をサポートするためのクライアント シークレットを使ったアプリ設定によって構成されます。 また、この Web アプリはローカル Git リポジトリのデプロイ ソースを使うように構成されます。 |
| | |

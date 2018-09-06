---
title: Azure CLI のサンプル - Azure SignalR Service | Microsoft Docs
description: Azure CLI のサンプル - Azure SignalR Service
services: signalr
documentationcenter: signalr
author: sffamily
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: signalr
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: signalr
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: a1ca02a5e058816c133accf3027f0a951db42cf2
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2018
ms.locfileid: "43664005"
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

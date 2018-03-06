---
title: "Azure Resource Manager テンプレートのサンプル - App Service | Microsoft Docs"
description: "Azure Resource Manager テンプレートのサンプル - App Service"
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
editor: ggailey777
tags: azure-service-management
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 02/26/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: e28a27b9a00164fcbba6eb5d3e5435548486ffa4
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2018
---
# <a name="azure-resource-manager-templates-for-azure-web-apps"></a>Azure Web Apps 用 Azure Resource Manager テンプレート

次の表は、Azure Resource Manager テンプレートのリンク一覧です。 Web アプリのテンプレートを作成するときに多く発生するエラーを回避するために推奨される事柄については、「[Guidance on deploying web apps with Azure Resource Manager templates (Azure Resource Manager テンプレートを使った Web アプリのデプロイに関するガイダンス)](web-sites-rm-template-guidance.md)」を参照してください。

| | |
|-|-|
|**Web アプリのデプロイ**||
| [GitHub リポジトリにリンクされた Web アプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| GitHub からコードを取得する Azure Web アプリをデプロイします。 |
| [カスタム展開スロットを使った Web アプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| カスタム展開スロット/環境を使って Azure Web アプリをデプロイします。 |
|**Web アプリの構成**||
| [Web アプリの証明書を Key Vault から取得](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Azure Web アプリの証明書を Key Vault シークレットからデプロイして SSL バインディングに使用します。 |
| [カスタム ドメインを使った Web アプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| カスタム ホスト名を使って Azure Web アプリをデプロイします。 |
| [カスタム ドメインと SSL を使った Web アプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| カスタム ホスト名を使って Azure Web アプリをデプロイし、Web アプリの証明書を Key Vault から取得して SSL バインディングに使用します。 |
| [GoLang 拡張機能を使った Web アプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Golang で開発された Web アプリケーションを Azure で実行することのできる Golang サイト拡張機能を使って Azure Web アプリをデプロイします。 |
| [Java 8 と Tomcat 8 を使った Web アプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Java 8 と Tomcat 8 に対応した Azure Web アプリをデプロイします。Java アプリケーションを Azure で実行することができます。 |
|**Linux Web アプリ**||
| [MySQL を使った Linux 上の Web アプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Azure Database for MySQL を使って Linux 上に Azure Web アプリをデプロイします。 |
| [PostgreSQL を使った Linux 上の Web アプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Azure Database for PostgreSQL を使って Linux 上に Azure Web アプリをデプロイします。 |
|**接続リソースを使った Web アプリ**||
| [MySQL を使った Web アプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Azure Database for MySQL を使って Windows 上に Azure Web アプリをデプロイします。 |
| [PostgreSQL を使った Web アプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Azure Database for PostgreSQL を使って Windows 上に Azure Web アプリをデプロイします。 |
| [SQL Database を使った Web アプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Azure Web アプリと SQL Database を "Basic" サービス レベルでデプロイします。 |
| [Blob Storage 接続を使った Web アプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Blob Storage の接続文字列を使って Azure Web アプリをデプロイします。Web アプリから Azure Blob Storage を使用できます。 |
| [Redis Cache を使った Web アプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Redis Cache を使って Azure Web アプリをデプロイします。 |
|**App Service 環境**||
| [App Service Environment v2 の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | App Service Environment v2 を仮想ネットワークに作成します。 |
| [ILB アドレスを使った App Service Environment v2 の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | プライベート内部ロード バランサー アドレスを使って仮想ネットワークに App Service Environment v2 を作成します。 |
| [ILB ASE または ILB ASE v2 に使用する既定の SSL 証明書の構成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | ILB App Service Environment または ILB App Service Environment v2 に使用する既定の SSL 証明書を構成します。 |
| | |

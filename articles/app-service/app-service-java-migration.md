---
title: Java アプリを Azure App Service に移行する
description: Azure App Service に使用できる Java 移行リソースを検出します。
author: msangapu-msft
ms.topic: article
ms.date: 03/29/2021
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: f876445673d0237af33f10e3e5b599032ba28bd7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092192"
---
# <a name="java-migration-resources-for-azure-app-service"></a>Azure App Service の Java 移行リソース

Azure App Service には、オンプレミスの Web サーバーにデプロイされた Web アプリを検出するためのツールが用意されています。 これらのアプリの準備状況を評価してから、アプリを App Service に移行することができます。 Web アプリのコンテンツとサポートされている構成の両方を、App Service に移行することができます。 これらのツールは、検出、評価、移行に重点を置いて、さまざまな種類のシナリオをサポートするために開発されています。

## <a name="java-tomcat-migration-linux"></a>Java Tomcat の移行 (Linux)

[アシスタントをダウンロード](https://azure.microsoft.com/services/app-service/migration-assistant/)して、Apache Tomcat Web サーバーで実行されている Java アプリを移行します。 また、Azure Container Registry を使用して、オンプレミスの Linux Docker コンテナーを App Service に移行することもできます。

| リソース |
|-----------|
| **方法** |
| [Java App-Service-Migration-Assistant Wiki](https://github.com/Azure/App-Service-Migration-Assistant/wiki/TOMCAT-Java-Information) |
| [Azure/App-Service-Migration-Assistant Wiki](https://github.com/Azure/App-Service-Migration-Assistant/wiki/Linux-Notes) |
| <bpt id="p1">[</bpt>ビデオ<ept id="p1">](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/featured)</ept> |
|[移行システムを使用して、Java アプリを Azure App Service にポイントして移行する](https://www.youtube.com/watch?v=Mpxa0KE0X9k) |

## <a name="standalone-tomcat-web-app-migration-windows-os"></a>スタンドアロンの Tomcat Web アプリの移行 (Windows OS)

この[プレビュー ツール](https://azure.microsoft.com/services/app-service/migration-assistant/)をダウンロードして、Apache Tomcat 上の Java Web アプリを Windows 上の App Service に移行します。 詳細については、[ビデオ](https://channel9.msdn.com/Shows/The-Launch-Space/Updates-on-Migrating-to-Azure-App-Service)と[方法](https://github.com/Azure/App-Service-Migration-Assistant/wiki/TOMCAT-Java-Information)を参照してください。

## <a name="containerize-standalone-tomcat-web-app"></a>スタンドアロンの Tomcat Web アプリをコンテナー化する

App Containerization には、最小限のコード変更で、アプリケーションをコンテナーとして再パッケージ化する簡単な方法があります。 このツールは現在、ASP.NET アプリケーションと Apache Tomcat Java Web アプリケーションのコンテナー化をサポートしています。 詳細については、[ブログ](https://azure.microsoft.com/blog/accelerate-application-modernization-with-azure-migrate-app-containerization/)と[方法](../migrate/tutorial-app-containerization-java-app-service.md)を参照してください。

## <a name="next-steps"></a>次の手順

[Tomcat アプリケーションを Azure App Service 上の Tomcat に 移行する](/azure/developer/java/migration/migrate-tomcat-to-tomcat-app-service)

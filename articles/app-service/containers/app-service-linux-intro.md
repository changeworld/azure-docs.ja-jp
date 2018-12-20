---
title: App Service on Linux の概要 - Azure | Microsoft Docs
description: Azure App Service on Linux について説明します。
keywords: Azure App Service, Linux, OSS
services: app-service
documentationcenter: ''
author: naziml
manager: cfowler
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/09/2018
ms.author: wesmc
ms.custom: seodec18
ms.openlocfilehash: ac8d5ddb843227e5c5d8e7508c3ea46946f4850e
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2018
ms.locfileid: "53257881"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Azure App Service on Linux の概要

[Web App](../app-service-web-overview.md) は、Web サイトと Web アプリケーションをホストするために最適化された、フル マネージドのコンピューティング プラットフォームです。 顧客は App Service on Linux を使用して、サポートされているアプリケーション スタック向けに Web アプリを Linux 上でネイティブにホストすることができます。 次のセクションは、現在サポートされているアプリケーション スタックの一覧です。

## <a name="languages"></a>Languages

App Service on Linux では、開発者の生産性を向上させるために、多数の組み込みイメージがサポートされています。 アプリケーションに必要なランタイムが組み込みイメージでサポートされていない場合は、[独自の Docker イメージを作成](tutorial-custom-docker-image.md)して Web App for Containers にデプロイする方法があります。

| 言語 | サポートされているバージョン |
|---|---|
| Node.js | 4.4、4.5、4.8、6.2、6.6、6.9、6.10、6.11、8.0、8.1、8.2、8.8、8.9、8.11、9.4、10.1、10.10 |
| Java * | Tomcat 8.5、9.0、Java SE、WildFly 14 (いずれも JRE 8 を実行) |
| PHP | 5.6、7.0、7.2 |
| Python (プレビュー) | 3.6、3.7 |
| .NET Core | 1.0、1.1、2.0、2.1 |
| Ruby | 2.3 |

詳細については、「[App Service on Linux で Java Web アプリを作成する](https://docs.microsoft.com/azure/app-service/containers/quickstart-java)」を参照してください。

## <a name="deployments"></a>デプロイメント

* FTP
* ローカル Git
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* ステージング環境
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) と DockerHub CI/CD

## <a name="console-publishing-and-debugging"></a>コンソール、公開、およびデバッグ

* 環境
* デプロイメント
* 基本コンソール
* SSH

## <a name="scaling"></a>スケーリング

* [App Service プラン](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json)のレベルを変更することで、Web アプリのスケールアップとスケールダウンを実行できます。

## <a name="locations"></a>場所

[Azure ステータス ダッシュボード](https://azure.microsoft.com/status)を確認します。

## <a name="limitations"></a>制限事項

Azure Portal では、Web App for Containers で現在使用できる機能のみが表示されます。 他の機能は、有効になったときにポータルに表示されるようになります。

仮想ネットワーク統合、Azure Active Directory/サード パーティの認証、Kudu サイト拡張機能などの機能は、まだ利用できません。 これらの機能が利用可能になったら、ドキュメントとブログを更新し、変更についてお知らせします。

App Service on Linux は [Basic、Standard、および Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) App Service プランでのみサポートされており、[Free または Shared](https://azure.microsoft.com/pricing/details/app-service/plans/) レベルはありません。 Web App for Containers は、非 Linux Web Apps で既にホストされている App Service プランでは作成できません。 Windows アプリと Linux アプリを同じリソース グループ内でも混在させないという点において、現在の制限があります。

## <a name="troubleshooting"></a>トラブルシューティング

アプリケーションが起動しない場合、またはアプリのログ記録を調べる場合は、LogFiles ディレクトリの Docker のログを確認してください。 このディレクトリには、SCM サイトまたは FTP 経由でアクセスできます。
コンテナーから `stdout` および `stderr` をログ記録するには、**[診断ログ]** で **[Docker Container ログ]** を有効にする必要があります。

![ログ記録の有効化][2]

![Kudu を使用した Docker のログの表示][1]

SCM サイトには、**[開発ツール]** メニューの **[Advanced Tools]** からアクセスできます。

## <a name="next-steps"></a>次の手順

以下の記事では、さまざまな言語で記述した Web アプリを使用して App Service on Linux での作業を開始することができます。

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/en-us/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [複数コンテナー アプリ](quickstart-multi-container.md)

App Service on Linux の詳細については、以下の記事も参照してください。

* [App Service on Linux の FAQ](app-service-linux-faq.md)
* [App Service on Linux での SSH のサポート](app-service-linux-ssh-support.md)
* [App Service でステージング環境を設定する](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Docker Hub の継続的なデプロイ](app-service-linux-ci-cd.md)

質問や問題は、[フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)に投稿できます。

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png

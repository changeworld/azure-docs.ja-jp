---
title: "Azure App Service Web App for Containers の FAQ | Microsoft Docs"
description: "Azure App Service Web App for Containers の FAQ"
keywords: "Azure App Service, Web アプリ, FAQ, Linux, OSS"
services: app-service
documentationCenter: 
author: ahmedelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: aelnably;wesmc
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 146c598f5cb62612327ce679a6bfaadc8312b149
ms.contentlocale: ja-jp
ms.lasthandoff: 09/14/2017

---
# <a name="azure-app-service-web-app-for-containers-faq"></a>Azure App Service Web App for Containers の FAQ

Web App for Containers のリリースでは、機能の追加とプラットフォームの向上に取り組んでいます。 この数か月の間にお客様からよく寄せられた質問 (FAQ) を以下にいくつか示します。
ご質問がある場合は、この記事の最後にあるコメント欄をご利用ください。できるだけ早く回答いたします。

## <a name="built-in-images"></a>組み込まれているイメージ

**Q:** プラットフォームに用意されている組み込みの Docker コンテナーをフォークしたいと思っています。 これらのファイルはどこで入手できますか。

**A:** すべての Docker ファイルは [GitHub](https://github.com/azure-app-service) にあります。 すべての Docker コンテナーは [Docker Hub](https://hub.docker.com/u/appsvc/) にあります。

**Q:** ランタイム スタックを構成する場合、[スタートアップ ファイル] セクションではどのような値が有効ですか。

**A:** Node.Js の場合は、PM2 構成ファイルまたはスクリプト ファイルを指定します。 .NET Core の場合は、コンパイル済みの DLL 名を指定します。 Ruby の場合は、アプリの初期化に使用する Ruby スクリプトを指定できます。

## <a name="management"></a>管理

**Q:** Azure Portal で [再起動] ボタンを押すと何が起こりますか。

**A:** これは Docker の再起動時に相当します。

**Q:** アプリ コンテナーの仮想マシン (VM) への接続に Secure Shell (SSH) を使用できますか。

**A:**はい、SCM サイト経由で可能です。

**Q:** Linux App Service プランを SDK または ARM テンプレートを使用して作成する方法を教えてください。

**A:** アプリ サービスの `reserved` フィールドを `true` に設定する必要があります。

## <a name="continuous-integrationdeployment"></a>継続的インテグレーション/デプロイ

**Q:** 自分の Web アプリでは、Docker Hub 上のイメージを更新した後も、古い Docker コンテナー イメージを引き続き使用しています。 カスタム コンテナーの継続的な統合/デプロイはサポートしていますか。

**A:** Azure Container Registry または DockerHub イメージの継続的インテグレーション/デプロイをセットアップするには、「[Continuous Deployment with Azure Web Apps for Containers (Azure Web App for Containers での継続的なデプロイ)](./app-service-linux-ci-cd.md)」を参照してください。 プライベート レジストリでは、Web アプリを停止してから起動することでコンテナーを更新できます。 または、ダミー アプリケーション設定を変更または追加して、コンテナーを強制的に更新できます。

**Q:** ステージング環境はサポートしていますか。

**A:** はい。

**Q:** **Web デプロイ**を使用して Web アプリをデプロイすることはできますか。

**A:** はい。`WEBSITE_WEBDEPLOY_USE_SCM` というアプリ設定を `false` に設定する必要があります。

## <a name="language-support"></a>言語のサポート

**Q:** コンパイルされていない .NET Core アプリはサポートされていますか。

**A:** はい。

**Q:** PHP アプリの依存関係マネージャーとして Composer はサポートされていますか。

**A:** はい。 Git のデプロイ中に、Kudu は (composer.lock ファイルの存在により) PHP アプリケーションをデプロイしていることを検出し、Composer のインストールを自動的にトリガーします。

## <a name="custom-containers"></a>カスタム コンテナー

**Q:** 自分が所有するカスタム コンテナーを使用しています。 私のアプリは `/home/` ディレクトリに存在しますが、[SCM サイト](https://github.com/projectkudu/kudu)や FTP クライアントを使用して内容を参照してもファイルが見つかりません。 ファイルはどこにありますか?

**A:** SMB 共有は `/home/` ディレクトリにマウントされます。 これにより、その場所にあるコンテンツが上書きされます。

**Q:** 自分が所有するカスタム コンテナーを使用しています。 プラットフォームを SMB 共有の `/home/` ディレクトリにマウントさせたいと考えています。

**A:**`WEBSITES_ENABLE_APP_SERVICE_STORAGE` アプリ設定を `true` に設定するか、またはアプリ設定を完全に削除することで、マウントできます。 これにより、プラットフォームのストレージの変更時に、コンテナーの再起動が行われることに注意してください。 

なお、WEBSITES_ENABLE_APP_SERVICE_STORAGE が 'false' の場合、/home/ ディレクトリはスケール インスタンス間で共有されなくなり、このディレクトリに書き込まれたファイルは再起動後には保持されません。

**Q:** カスタム コンテナーの起動に時間がかかり、起動が終了する前にプラットフォームがコンテナーを再起動します。

**A:** プラットフォームがコンテナーを再起動する前の待機時間を構成できます。 これは、`WEBSITES_CONTAINER_START_TIME_LIMIT` アプリ設定を目的の値 (秒単位) に設定することで実現できます。 既定値は 230 秒であり、最大値は 600 秒です。

**Q:**プライベート レジストリ サーバーの URL の形式は何ですか。

**A:** `http://` または `https://` を含む完全なレジストリ URL を入力する必要があります。

**Q:**プライベート レジストリ オプションのイメージ名の形式は何ですか。

**A:** プライベート レジストリの URL を含む完全なイメージ名を追加する必要があります (例:  myacr.azurecr.io/dotnet:latest). カスタム ポートを使用したイメージ名は、[ポータル経由で入力することはできません](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650)。 [`az`コマンド ライン ツールを使用して、](https://docs.microsoft.com/en-us/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set) `docker-custom-image-name`を設定してください。

**Q:** カスタム コンテナー イメージで 1 つ以上のポートを公開したいと思っています。 可能でしょうか。

**A:** 現時点ではサポートされていません。

**Q:** 自分が所有するストレージを持ち込むことはできますか?

**A:** 現時点ではサポートされていません。

**Q:** SCM サイトからカスタム コンテナーのファイル システムや実行中のプロセスを参照できません。 なぜですか?

**A:** SCM サイトは別のコンテナーで実行されています。 アプリ コンテナーのファイル システムや実行中のプロセスをチェックすることはできません。

**Q:** カスタム コンテナーがポート 80 以外のポートをリッスンしています。 そのポートに要求をルーティングするようにアプリを構成するにはどうすればよいですか。

**A:** ポートの検出は自動化されていますが、**WEBSITES_PORT** というアプリケーション設定を指定して必要なポート番号の値を設定することもできます。 以前はプラットフォームが `PORT` アプリ設定を使用していましたが、このアプリ設定の使用は止めて、`WEBSITES_PORT` だけを使用するようにする変更を予定しています。

**Q:** カスタム コンテナーに HTTPS を実装する必要がありますか。

**A:** いいえ、共有フロント エンドでの HTTPS の終了はプラットフォームが処理します。

## <a name="pricing-and-sla"></a>料金と SLA

**Q:**一般的にサービスが利用できる現在の料金を教えてください。

**A:** アプリの実行時間に対して、通常の Azure App Service の料金が課金されます。

## <a name="other"></a>その他

**Q:** アプリの設定名でサポートされる文字は何ですか。

**A:** アプリケーション設定では、A から Z、a から z、0 から 9、およびアンダースコアのみを使用できます。

**Q:** 新機能はどこでリクエストできますか。

**A:** [Web Apps フィードバック フォーラム](https://aka.ms/webapps-uservoice)で自分のアイデアを送信できます。 アイデアのタイトルに "[Linux]" を追加してください。

## <a name="next-steps"></a>次のステップ

* [Azure Web App for Containers とは](app-service-linux-intro.md)
* [Azure App Service でステージング環境を設定する](../../app-service-web/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Azure Web App for Containers での継続的なデプロイ](./app-service-linux-ci-cd.md)


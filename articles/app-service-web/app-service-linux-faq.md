---
title: "Azure App Service Web Apps on Linux のFAQ | Microsoft Docs"
description: "Azure App Service Web App on Linux の FAQ です。"
keywords: "Azure App Service, Web アプリ, FAQ, Linux, OSS"
services: app-service
documentationCenter: 
authors: ahmedelnably
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
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 3fb0f6b26f36ef4044c9733ace05c4f59909ddda
ms.contentlocale: ja-jp
ms.lasthandoff: 06/16/2017


---

# <a name="azure-app-service-web-app-on-linux-faq"></a>Azure App Service Web App on Linux の FAQ

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


Azure App Service on Linux のリリースでは、機能の追加とプラットフォームの向上に取り組んでいます。 この数か月の間にお客様からよく寄せられた質問 (FAQ) を以下にいくつか示します。
ご質問がある場合は、この記事の最後にあるコメント欄をご利用ください。できるだけ早く回答いたします。

## <a name="built-in-images"></a>組み込まれているイメージ

**Q:** プラットフォームに用意されている組み込みの Docker コンテナーをフォークしたいと思っています。 これらのファイルはどこで入手できますか。

**A:** すべての Docker ファイルは [GitHub](https://github.com/azure-app-service) にあります。 すべての Docker コンテナーは [Docker Hub](https://hub.docker.com/u/appsvc/) にあります。

**Q:** ランタイム スタックを構成する場合、[スタートアップ ファイル] セクションではどのような値が有効ですか。

**A:** Node.Js の場合は、PM2 構成ファイルまたはスクリプト ファイルを指定します。 .NET Core の場合は、コンパイル済みの DLL 名を指定します。 Ruby の場合は、アプリの初期化に使用する Ruby スクリプトを指定できます。

## <a name="management"></a>管理

**Q:** Azure ポータルで [再起動] ボタンを押すと何が起こりますか。

**A:** これは Docker の再起動時に相当します。

**Q:** アプリ コンテナーの仮想マシン (VM) への接続に Secure Shell (SSH) を使用できますか。

**A:** はい、SCM サイトを通して行うことができます。詳細については、「[Web App on Linux での SSH のサポート](./app-service-linux-ssh-support.md)」を参照してください。

## <a name="continuous-integrationdeployment"></a>継続的インテグレーション/デプロイ

**Q:** 自分の Web アプリでは、Docker Hub 上のイメージを更新した後も、古い Docker コンテナー イメージを引き続き使用しています。 カスタム コンテナーの継続的な統合/デプロイはサポートしていますか。

**A:** DockerHub イメージの継続的な統合/デプロイを設定するには、「[Web App on Linux での Docker Hub の継続的なデプロイ](./app-service-linux-ci-cd.md)」を参照してください。 プライベート レジストリでは、Web アプリを停止してから起動することでコンテナーを更新できます。 または、ダミー アプリケーション設定を変更または追加して、コンテナーを強制的に更新できます。

**Q:** ステージング環境はサポートしていますか。

**A:** はい。

**Q:** **Web デプロイ**を使用して Web アプリをデプロイすることはできますか。

**A:** はい。`UseWebDeployScm` というアプリ設定を `false` に設定する必要があります。

## <a name="language-support"></a>言語のサポート

**Q:** コンパイルされていない .NET Core アプリはサポートされていますか。

**A:** はい。

**Q:** PHP アプリの依存関係マネージャーとして Composer はサポートされていますか。

**A:** はい。 Git のデプロイ中に、Kudu は (composer.json ファイルの存在により) PHP アプリケーションをデプロイしていることを検出し、Composer のインストールを自動的にトリガーします。

## <a name="custom-containers"></a>カスタム コンテナー

**Q:** 自分が所有するカスタム コンテナーを使用しています。 私のアプリは `\home\` ディレクトリに存在しますが、[SCM サイト](https://github.com/projectkudu/kudu)や FTP クライアントを使用して内容を参照してもファイルが見つかりません。 ファイルはどこにありますか?

**A:** SMB 共有は `\home\` ディレクトリにマウントされます。 これにより、その場所にあるコンテンツが上書きされます。

**Q:**プライベート レジストリ サーバーの URL の形式は何ですか。

**A:** `http://` または `https://` を含む完全なレジストリ URL を入力する必要があります。

**Q:**プライベート レジストリ オプションのイメージ名の形式は何ですか。

**A:** プライベート レジストリ の URI を含む完全なイメージ名を追加する必要があります (例:  myacr.azurecr.io/dotnet:latest)。

**Q:** カスタム コンテナー イメージで 1 つ以上のポートを公開したいと思っています。 可能でしょうか。

**A:** 現時点ではサポートされていません。

**Q:** 自分が所有するストレージを持ち込むことはできますか?

**A:** 現時点ではサポートされていません。

**Q:** SCM サイトからカスタム コンテナーのファイル システムや実行中のプロセスを参照できません。 なぜですか?

**A:** SCM サイトは別のコンテナーで実行されています。 アプリ コンテナーのファイル システムや実行中のプロセスをチェックすることはできません。

**Q:** カスタム コンテナーがポート 80 以外のポートをリッスンしています。 そのポートに要求をルーティングするようにアプリを構成するにはどうすればよいですか。

**A:** ポートの検出は自動化されていますが、**PORT** というアプリケーション設定を指定して必要なポート番号の値を設定することもできます。

**Q:** カスタム コンテナーに HTTPS を実装する必要がありますか。

**A:** いいえ、共有フロント エンドでの HTTPS の終了はプラットフォームが処理します。

## <a name="pricing-and-sla"></a>料金と SLA

**Q:** パブリック プレビューを使用しているときの料金はいくらですか。

**A:** アプリの実行時間の1/2 に対して、通常の Azure App Service の料金が課金されます。 つまり、通常の Azure App Service の料金から 50% 割引になります。

## <a name="other"></a>その他

**Q:** アプリの設定名でサポートされる文字は何ですか。

**A:** アプリケーション設定では、A から Z、a から z、0 から 9、およびアンダースコアのみを使用できます。

**Q:** 新機能はどこでリクエストできますか。

**A:** [Web Apps フィードバック フォーラム](https://aka.ms/webapps-uservoice)で自分のアイデアを送信できます。 アイデアのタイトルに "[Linux]" を追加してください。

## <a name="next-steps"></a>次のステップ
* [Azure Web App on Linux とは](app-service-linux-intro.md)
* [Azure Web App on Linux で Web アプリを作成する](app-service-linux-how-to-create-web-app.md)
* [Azure Web App on Linux での SSH のサポート](./app-service-linux-ssh-support.md)
* [Azure App Service でステージング環境を設定する](./web-sites-staged-publishing.md)
* [Azure Web App on Linux での Docker Hub の継続的なデプロイ](./app-service-linux-ci-cd.md)


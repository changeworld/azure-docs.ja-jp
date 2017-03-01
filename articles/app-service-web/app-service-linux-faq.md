---
title: "Azure App Service Web Apps on Linux のFAQ | Microsoft Docs"
description: "Azure App Service Web Apps on Linux のよく寄せられる質問 (FAQ) です。"
keywords: "Azure App Service, Web アプリ, FAQ, Linux, OSS"
services: app-service
documentationCenter: 
authors: aelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 831ef097027721146531e8d699fe3f67417a57ea
ms.openlocfilehash: b88aa3d0ae89aec81c2b9144fb5de3210a0b8d1e
ms.lasthandoff: 02/18/2017


---

# <a name="azure-app-service-web-apps-on-linux-faq"></a>Azure App Service Web Apps on Linux のFAQ #

Azure App Service on Linux (現在プレビュー段階) のリリースでは、機能の追加とプラットフォームの向上に取り組んでいます。 この数か月の間にお客様からよく寄せられた質問を以下に示します。
ご質問がある場合は、この記事の最後にあるコメント欄をご利用ください。できるだけ早く回答いたします。

## <a name="built-in-images"></a>組み込まれているイメージ ##

**Q:** プラットフォームに用意されている組み込みの Docker コンテナーをフォークしたいと思っています。 これらのファイルはどこで入手できますか。

**A:** すべての Docker ファイルは https://github.com/azure-app-service で見つけることができます。 すべての Docker コンテナーは https://hub.docker.com/u/appsvc/ で見つけることができます。

## <a name="management"></a>管理 ##

**Q:** ポータルで [再起動] ボタンを押しましたが、Web アプリが再起動しません。なぜですか?

**A:** 近い将来、リセット ボタンを有効にすることに取り組んでいます。現時点では、次の&2; つの選択肢があります。
1. ダミーのアプリケーション設定を追加するか変更します。これにより Web アプリが強制的に再起動します。 
2. Web アプリを停止してから起動します。

**Q:** VM への SSH 接続は可能ですか。

**A:** いいえ。今後のリリースで、アプリ コンテナーに SSH 接続する方法を提供する予定です。

## <a name="continuous-integration--deployment"></a>継続的な統合/デプロイ ##

**Q:** 自分の Web アプリでは、DockerHub 上のイメージを更新した後も、古い Docker コンテナー イメージを引き続き使用しています。 カスタム コンテナーの継続的な統合/デプロイはサポートしていますか。

**A:** コンテナーの更新は、Web アプリを停止して起動するか、ダミーのアプリケーション設定を変更/追加してコンテナーを強制的に更新することで実行できますが、今後のリリースで、カスタム コンテナー用の CI/CD 機能が提供される予定です。

## <a name="language-support"></a>言語のサポート ##

**Q:** コンパイルされていない .net core アプリはサポートされていますか。

**A:** いいえ。コンパイル済みの .net コア アプリとすべての依存関係をデプロイする必要があります。フル デプロイとビルド エクスペリエンスは、今後のリリースで提供される予定です。

## <a name="built-in-images"></a>組み込まれているイメージ ##

**Q:** ランタイム スタックを構成する場合、[スタートアップ ファイル] セクションではどのような値が有効ですか。

**A:** Node.Js の場合、PM2 構成ファイルまたはスクリプト ファイルを指定できます。 .Net Core の場合、コンパイル済みの dll 名を指定する必要があります。 Ruby の場合、アプリの初期化に使用する Ruby スクリプトを指定できます。

## <a name="custom-containers"></a>カスタム コンテナー ##

**Q:** 自分が所有するカスタム コンテナーを使用しています。 私のアプリは \home\ ディレクトリに存在しますが、SCM サイトや ftp クライアントを使用して内容を参照してもファイルが見つかりません。 ファイルはどこにありますか?

**A:** SMB 共有は \home\ ディレクトリにマウントされます。そのため、その場所にあるすべての内容はオーバーライドされます。

**Q:** カスタム コンテナー イメージで&1; つ以上のポートを公開したいと思っています。 可能でしょうか。

**A:** 現時点では、それはサポートされていません。

**Q:** 自分が所有するストレージを持ち込むことはできますか?

**A:** 現時点では、それはサポートされていません。

**Q:** SCM サイトからカスタム コンテナーのファイル システムや実行中のプロセスを参照できません。 なぜですか?

**A:** SCM サイトは別のコンテナーで実行されています。アプリ コンテナーのファイル システムや実行中のプロセスをチェックすることはできません。

**Q:** カスタム コンテナーがポート 80 以外のポートをリッスンしています。 そのポートに要求をルーティングするようにアプリを構成するにはどうすればよいですか。

**A:** **PORT** というアプリケーション設定を指定して、必要なポート番号の値を設定できます。

## <a name="pricing-and-sla"></a>料金と SLA ##

**Q:** パブリック プレビュー中の料金はいくらですか。

**A:** アプリの実行時間の 2 分の 1 に対して、Azure App Service の通常料金が課金されます。実質的に Azure App Service の通常料金の 50% 割引を意味します。

## <a name="other"></a>その他 ##

**Q:** アプリの設定名でサポートされる文字は何ですか。

**A:** アプリの設定では、A ～ Z、a ～ z、0 ～ 9、およびアンダースコアのみを使用できます。

**Q:** 新機能はどこでリクエストできますか。

**A:**: https://aka.ms/webapps-uservoice にアイデアを送ることができます。 アイデアのタイトルに [Linux] を追加してください。

## <a name="next-steps"></a>次のステップ
* [App Service on Linux とは](app-service-linux-intro.md)
* [App Service on Linux での Web Apps の作成](app-service-linux-how-to-create-a-web-app.md)


---
title: "Azure App Service Web Apps on Linux のFAQ | Microsoft Docs"
description: "Azure App Service Web Apps on Linux のよく寄せられる質問 (FAQ) です。"
keywords: "Azure App Service, Web アプリ, FAQ, Linux, OSS"
services: app-service
documentationCenter: 
authors: aelnably
manager: wpickett
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
ms.sourcegitcommit: d4a5260dde1994bbf4ef4467eddd2fb80a2199b0
ms.openlocfilehash: 22c6c22ba3123555d279fac087a6be2f99bde8d7


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

**A:** いいえ。近い将来、アプリ コンテナーに SSH 接続する方法を提供する予定です。

## <a name="continous-integration--deployment"></a>継続的な統合/デプロイ ##

**Q:** 自分の Web アプリでは、DockerHub 上のイメージを更新した後も、古い Docker コンテナー イメージを引き続き使用しています。 カスタム コンテナーの継続的な統合/デプロイはサポートしていますか。

**A:** コンテナーの更新は、Web アプリを停止して起動するか、ダミーのアプリケーション設定を変更/追加してコンテナーを強制的に更新することで実行できますが、近い将来、カスタム コンテナー用の CI/CD 機能が提供される予定です。

## <a name="language-support"></a>言語のサポート ##

**Q:** コンパイルされていない .net core アプリはサポートされていますか。

**A:** いいえ。コンパイル済みの .net コア アプリとすべての依存関係をデプロイする必要があります。フル デプロイとビルド エクスペリエンスは、近い将来提供される予定です。

## <a name="custom-containers"></a>カスタム コンテナー ##

**Q:** 自分が所有するカスタム コンテナーを使用しています。 私のアプリは \home\ ディレクトリに存在しますが、SCM サイトや ftp クライアントを使用して内容を参照してもファイルが見つかりません。 ファイルはどこにありますか?

**A:** SMB 共有は \home\ ディレクトリにマウントされます。そのため、その場所にあるすべての内容はオーバーライドされます。

**Q:** カスタム コンテナー イメージで&1; つ以上のポートを公開したいと思っています。 可能でしょうか。

**A:** 現時点では、それはサポートされていません。

**Q:** 自分が所有するストレージを持ち込むことはできますか?

**A:**現時点ではサポートされていませんが、近い将来のサポートが計画されています。

**Q:** SCM サイトからカスタム コンテナーのファイル システムや実行中のプロセスを参照できません。 なぜですか?

**A:** SCM サイトは別のコンテナーで実行されています。アプリ コンテナーのファイル システムや実行中のプロセスをチェックすることはできません。

## <a name="pricing-and-sla"></a>料金と SLA ##

**Q:** パブリック プレビュー中の料金はいくらですか。

**A:** アプリの実行時間の 2 分の 1 に対して、Azure App Service の通常料金が課金されます。実質的に Azure App Service の通常料金の 50% 割引を意味します。

## <a name="other"></a>その他 ##

**Q:** アプリの設定名でサポートされる文字は何ですか。

**A:** アプリの設定では、A ～ Z、a ～ z、0 ～ 9、およびアンダースコアのみを使用できます。

**Q:** 新機能はどこでリクエストできますか。

**A:**: https://aka.ms/webapps-uservoice にアイデアを送ることができます。 アイデアのタイトルに [Linux] を追加してください。

## <a name="next-steps"></a>次のステップ
* [App Service on Linux の概要](./app-service-linux-intro.md) 
* [App Service on Linux とは](app-service-linux-intro.md)
* [App Service on Linux での Web Apps の作成](./app-service-linux-how-to-create-a-web-app.md)



<!--HONumber=Feb17_HO3-->



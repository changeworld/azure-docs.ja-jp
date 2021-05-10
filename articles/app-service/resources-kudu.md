---
title: Kudu サービスの概要
description: App Service とその機能で継続的なデプロイを実現するエンジンについて説明します。
ms.date: 03/17/2021
ms.topic: reference
ms.openlocfilehash: ad1456f1ca123127f3d84aa8195c99fc34872aee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608741"
---
# <a name="kudu-service-overview"></a>Kudu サービスの概要

Kudu は、ソース管理ベースのデプロイに関連する [Azure App Service](overview.md) のさまざまな機能の背後にあるエンジンであり、Dropbox や OneDrive の同期などの他のデプロイ方法にも対応しています。 

## <a name="access-kudu-for-your-app"></a>アプリの Kudu にアクセスする
アプリを作成するたびに、App Service によって、HTTPS によって保護されるコンパニオン アプリが作成されます。 この Kudu アプリには次の場所からアクセスできます。

- アプリが分離レベルにありません: `https://<app-name>.scm.azurewebsites.net`
- 分離レベルのアプリ (App Service Environment): `https://<app-name>.scm.<ase-name>.p.azurewebsites.net`

詳細については、「[Kudu サービスへのアクセス](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)」に関するページを参照してください。

## <a name="kudu-features"></a>Kudu の機能

Kudu は、次のような App Service アプリに関する有益な情報を提供します。

- アプリの設定
- 接続文字列
- 環境変数
- サーバー変数
- HTTP ヘッダー

また、次のような他の機能も用意されています。

- [Kudu コンソール](https://github.com/projectkudu/kudu/wiki/Kudu-console)でコマンドを実行します。
- IIS 診断ダンプまたは Docker ログをダウンロードします。
- IIS のプロセスとサイト拡張機能を管理します。
- Windows aps の展開 Webhook を追加します。
- `/ZipDeploy` で ZIP 展開 UI を使用できるようにします。
- [カスタム デプロイ スクリプト](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)を生成します。
- [REST API](https://github.com/projectkudu/kudu/wiki/REST-API) によるアクセスを許可します。

## <a name="more-resources"></a>その他のリソース

Kudu は [オープン ソース プロジェクト](https://github.com/projectkudu/kudu)であり、[Kudu Wiki](https://github.com/projectkudu/kudu/wiki) にドキュメントがあります。


---
title: App Service on Azure Stack Update 1 | Microsoft Docs
description: App Service on Azure Stack Update 1 の内容、既知の問題、更新プログラムをダウンロードする場所について説明します。
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 0c33c8fdefbb27ba8414e58bed1b42ee7aaba88a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="app-service-on-azure-stack-update-one-release-notes"></a>App Service on Azure Stack Update 1 のリリース ノート

*適用先: Azure Stack 統合システムと Azure Stack Development Kit*

これらのリリース ノートでは、Azure App Service on Azure Stack Update 1 における機能強化と修正点、および既知の問題について説明します。 既知の問題は、デプロイおよび更新プロセスに直接関係する問題と、ビルド (インストール後) に関する問題に分けられています。

> [!IMPORTANT]
> Azure App Service をデプロイする前に、Azure Stack 統合システムに 1802 更新プログラムを適用するか、または最新の Azure Stack 開発キットをデプロイします。
>
>

## <a name="build-reference"></a>ビルドのリファレンス

App Service on Azure Stack Update 1 のビルド番号は **69.0.13698.9** です。

### <a name="prerequisites"></a>前提条件

> [!IMPORTANT]
> Azure App Service で Kudu の SSO を処理する方法が改善されたため、Azure App Service on Azure Stack では、[3 サブジェクトのワイルドカード証明書](azure-stack-app-service-before-you-get-started.md#get-certificates)が必要になります。  新しいサブジェクトは、** *.sso.appservice.<region>.<domainname>.<extension>** です。
>
>

デプロイを開始する前に、[前提条件に関するドキュメント](azure-stack-app-service-before-you-get-started.md)をご覧ください。

### <a name="new-features-and-fixes"></a>新機能と修正

Azure App Service on Azure Stack Update 1 には、次の機能強化と修正が含まれています。

- **Azure App Service の高可用性** - Azure Stack 1802 更新プログラムにより、ワークロードを複数の障害ドメインにデプロイできるようになりました。  そのため、App Service インフラストラクチャは、複数の障害ドメインへのデプロイによってフォールト トレランスを実現できます。  既定では、Azure App Service のすべての新しいデプロイにこの機能が含まれます。ただし、Azure Stack 1802 更新プログラムを適用する前に完了したデプロイについては、[App Service の障害ドメインに関するドキュメント](azure-stack-app-service-fault-domain-update.md)をご覧ください。

- **既存の仮想ネットワークへのデプロイ** - App Service on Azure Stack を既存の仮想ネットワーク内にデプロイできるようになりました。  既存の仮想ネットワークにデプロイすると、Azure App Service に必要な SQL Server とファイル サーバーにプライベート ポート経由で接続できます。  デプロイ時に既存の仮想ネットワークへのデプロイを選択できますが、デプロイ前に、[App Service が使用するサブネットを作成](azure-stack-app-service-before-you-get-started.md#virtual-network)する必要があります。

- **App Service のテナント ポータル、管理ポータル、Functions ポータル、Kudu ツール**の更新。  Azure Stack Portal SDK バージョンと一致しています。

- **次のアプリケーション フレームワークとツールの更新**:
    - **.Net Core 2.0** のサポートが追加されました。
    - **Node.js** の次のバージョンが追加されました。
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - **NPM** の次のバージョンが追加されました。
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - 次の **PHP** 更新プログラムが追加されました。
        - 5.6.32
        - 7.0.26 (x86 および x64)
        - 7.1.12 (x86 および x64)
    - **Git for Windows** が v 2.14.1 に更新されました。
    - **Mercurial** が v4.5.0 に更新されました。

  - App Service テナント ポータルの [カスタム ドメイン] 機能内で **[HTTPS のみ]** 機能のサポートが追加されました。 

  - Azure Functions のカスタム ストレージ ピッカーにストレージ接続の検証が追加されました。 

#### <a name="fixes"></a>修正

- オフライン展開パッケージの作成時に、App Service インストーラーからフォルダーを開くときに、アクセス拒否エラー メッセージが表示されることはなくなります。

- App Service テナント ポータルの [カスタム ドメイン] 機能の使用時の問題が解決されました。

- セットアップ中に予約済みの管理者名を使用できなくなります。

- **ドメイン参加済み**ファイル サーバーで App Service デプロイメントが有効になりました。

- スクリプトでの Azure Stack のルート証明書の取得が改善され、App Service インストーラーでルート証明書が検証されるようになりました。

- Microsoft.Web 名前空間のリソースを含むサブスクリプションが削除されたときに、Azure Resource Manager に正しくない状態が返される問題が修正されました。

### <a name="known-issues-with-the-deployment-process"></a>デプロイ プロセスに関する既知の問題

- Azure App Service on Azure Stack Update 1 のデプロイに関する既知の問題はありません。

### <a name="known-issues-with-the-update-process"></a>更新プロセスに関する既知の問題

- Azure App Service on Azure Stack Update 1 の更新に関する既知の問題はありません。

### <a name="known-issues-post-installation"></a>既知の問題 (インストール後)

- Azure App Service on Azure Stack Update 1 のインストールに関する既知の問題はありません。

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Azure App Service on Azure Stack を運用するクラウド管理者に関する既知の問題

[Azure Stack 1802 リリース ノート](azure-stack-update-1802.md)内のドキュメントをご覧ください。

## <a name="see-also"></a>関連項目

- Azure App Service の概要については、「[Azure Stack 上の App Service の概要](azure-stack-app-service-overview.md)」をご覧ください。
- App Service on Azure Stack のデプロイの準備をする方法の詳細については、「[App Service on Azure Stack を開始する前に](azure-stack-app-service-before-you-get-started.md)」をご覧ください。

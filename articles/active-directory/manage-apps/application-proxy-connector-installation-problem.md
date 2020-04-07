---
title: アプリケーション プロキシ エージェント コネクタのインストール時の問題 | Microsoft Docs
description: アプリケーション プロキシ エージェント コネクタのインストール時に発生する可能性のある問題のトラブルシューティング方法
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 466e1ce0efbdec3f5475634f3857d02554d93d98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049128"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>アプリケーション プロキシ エージェント コネクタのインストール時の問題

Microsoft AAD アプリケーション プロキシ コネクタは、発信接続を使用して、クラウドで使用可能なエンドポイントから内部のドメインへの接続を確立する内部ドメイン コンポーネントです。

## <a name="general-problem-areas-with-connector-installation"></a>コネクタのインストールに関する一般的な問題領域

コネクタのインストールに失敗する場合、根本原因は通常、次の領域のいずれかにあります。

1.  **接続** – インストールを正常に完了するには、新しいコネクタを登録し、将来の信頼プロパティを確立する必要があります。 これは、AAD アプリケーション プロキシ クラウド サービスに接続することによって行います。

2.  **信頼の確立** – 新しいコネクタは、自己署名証明書を作成し、クラウド サービスに登録します。

3.  **管理者の認証** – コネクタのインストールを完了するために、ユーザーはインストール時に管理者の資格情報を提供する必要があります。

> [!NOTE]
> コネクタのインストール ログは %TEMP% フォルダーにあり、インストール エラーの原因に関する追加情報を提供するのに役立ちます。

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>クラウド アプリケーション プロキシ サービスと Microsoft のログイン ページへの接続を確認する

**目的:** コネクタ コンピューターが AAD アプリケーション プロキシの登録エンドポイントと Microsoft のログイン ページに接続できることを確認します。

1.  [telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) またはその他のポート テスト ツールを使用して、コネクタ サーバー上でポートのテストを実行して、ポート 443 と 80 が開いているかどうかを確認します。

2.  これらのポートのいずれかが正常に実行されない場合は、ファイアウォールまたはバックエンド プロキシが、必要なドメインおよびポートにアクセスできることを確認します。「[オンプレミスの環境を準備する](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)」を参照してください。

3.  ブラウザー (別のタブ) を開き、Web ページ `https://login.microsoftonline.com` に移動し、そのページにログインできることを確認します。

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>コンピューターとバックエンド コンポーネントでアプリケーション プロキシ信頼証明書がサポートされていることを確認する

**目的:** コネクタ コンピューターおよびバックエンドのプロキシとファイアウォールが将来の信頼のためにコネクタによって作成された証明書をサポートできることを確認します。

>[!NOTE]
>コネクタは、TLS1.2 でサポートされる SHA512 証明書の作成を試みます。 コンピューターまたはバックエンドのファイアウォールとプロキシで TLS1.2 がサポートされていない場合、インストールに失敗します。
>
>

**問題を解決するには、以下の操作を実行します。**

1.  コンピューターで TLS 1.2 がサポートされていることを確認します。2012 R2 より後のすべてのバージョンの Windows では、TLS1.2 をサポートしています。 コネクタ コンピューターで 2012 R2 以前のバージョンを使用している場合は、サポート技術情報 (<https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>) の更新プログラムがコンピューターにインストールされていることを確認してください。

2.  ネットワーク管理者に連絡し、バックエンドのプロキシとファイアウォールで発信トラフィックについて SHA512 がブロックされていないことを確認するよう依頼します。

## <a name="verify-admin-is-used-to-install-the-connector"></a>コネクタのインストールに管理者を使用していることを確認する

**目的:** コネクタをインストールするユーザーが適切な資格情報を持つ管理者であることを確認します。 現時点では、正常にインストールするには、そのユーザーは、少なくともアプリケーション管理者である必要があります。

**資格情報が適切であることを確認するには:**

`https://login.microsoftonline.com` に接続し、同じ資格情報を使用します。 ログインに成功したことを確認します。 **[Azure Active Directory]**  -&gt; **[ユーザーとグループ]**  -&gt; **[すべてのユーザー]** と移動すると、ユーザー ロールを確認できます。 

ユーザー アカウントを選択してから、表示されたメニューの [ディレクトリ ロール] を選択します。 選択されているロールが "アプリケーション管理者" であることを確認します。 これらの手順に従っても、どのページにもアクセスできない場合、ユーザーは必要なロールを持っていません。

## <a name="next-steps"></a>次のステップ
[Azure AD アプリケーション プロキシ コネクタについて](application-proxy-connectors.md)

---
title: "IIS の認証と Azure MFA サーバー |Microsoft ドキュメント"
description: "これは、IIS の認証と Azure Multi-factor Authentication Server の展開に役立つ Azure 多要素認証 ページです。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: d1bf1c8a-2c10-4ae6-9f4b-75f0c3df43eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/16/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: H1Hack27Feb2017,it-pro
ms.openlocfilehash: ab6f9110dccd3cfc15092f535650e8d8cb1af13c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="configure-azure-multi-factor-authentication-server-for-iis-web-apps"></a>IIS の web アプリの Azure Multi-factor Authentication Server を構成します。

有効にし、Microsoft IIS web アプリケーションと統合するための IIS 認証を構成するには、Azure Multi-factor Authentication (MFA) サーバーの IIS 認証 セクションを使用します。 Azure MFA サーバーが Azure Multi-factor Authentication を追加する IIS web サーバーに行われる要求をフィルターできるプラグインがインストールされます。 この IIS プラグインは、フォーム ベース認証と統合 Windows HTTP 認証のサポートを提供します。 信頼された 2 要素認証から内部 IP アドレスを除外する ip アドレスが構成もことができます。

![IIS 認証](./media/multi-factor-authentication-get-started-server-iis/iis.png)

## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>Azure の Multi-factor Authentication Server で IIS のフォーム ベース認証の使用
フォーム ベース認証を使用する IIS web アプリケーションをセキュリティで保護 IIS web サーバーに Azure Multi-factor Authentication Server をインストールし、次の手順でサーバーを構成します。

1. Azure の Multi-factor Authentication Server では、左側のメニューで [IIS 認証] アイコンをクリックします。
2. クリックして、**フォーム ベース**タブです。
3. **[追加]**をクリックします。
4. ユーザー名、パスワード、およびドメイン変数を自動的に検出し、[web サイト] ダイアログ ボックス内で (https://localhost/contoso/auth/login.aspx) のようなログイン URL を入力し、をクリックする**OK**です。
5. チェック、**を必要と Multi-factor Authentication ユーザーの一致**場合、すべてのユーザーがされているか、および multi-factor authentication の対象サーバーにインポートされます。 サーバーにまだインポートされていないユーザーや、多要素認証から除外されるユーザーの数が多い場合、ボックスをオフのままにします。
6. ページ変数を自動検出できない場合はクリックして**を手動で指定**[web サイト] ダイアログ ボックス。
7. フォームの web サイト ダイアログ ボックスで、送信 URL フィールドでログイン ページに、URL を入力し、(省略可能) アプリケーション名を入力します。 アプリケーション名は、Azure Multi-factor Authentication レポートに表示され、SMS またはモバイル アプリの認証メッセージに表示される可能性があります。
8. 正しい要求の形式を選択します。 これは、設定は**POST または GET**ほとんどの web アプリケーションです。
9. ([ログイン] ページに表示される) 場合は、ユーザー名変数、パスワード変数、およびドメイン変数を入力します。 入力ボックスの名前を検索するには、web ブラウザーでログイン ページに移動 ページを右クリックし、選択**ソースの表示**です。
10. チェック、**を必要と Azure Multi-factor Authentication ユーザーの一致**場合、すべてのユーザーがされているか、および multi-factor authentication の対象サーバーにインポートされます。 サーバーにまだインポートされていないユーザーや、多要素認証から除外されるユーザーの数が多い場合、ボックスをオフのままにします。
11. をクリックして**詳細**などの高度な設定を確認します。

  - カスタムの拒否ページファイルを選択します。
  - キャッシュの web サイトに成功した認証の cookie を使用して、期間
  - Windows ドメイン、LDAP ディレクトリに対してプライマリ資格情報を認証するかどうかを選択します。 または、RADIUS サーバー。

12. をクリックして**OK** [フォームの web サイト] ダイアログ ボックスに戻ります。
13. **[OK]**をクリックします。
14. URL とページの変数を自動検出するか、入力されている web サイトのデータをフォーム ベース ウィンドウに表示します。

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>Azure Multi-factor Authentication Server で統合 Windows 認証の使用
統合 Windows HTTP 認証を使用する IIS web アプリケーションをセキュリティで保護するには、IIS web サーバーに Azure MFA サーバーをインストールし、次の手順でサーバーを構成します。

1. Azure の Multi-factor Authentication Server では、左側のメニューで [IIS 認証] アイコンをクリックします。
2. クリックして、 **HTTP**タブです。
3. **[追加]**をクリックします。
4. ベース URL の追加 ダイアログ ボックスで、(http://localhost/owa) のように HTTP 認証が実行される web サイトの URL を入力し、(省略可能) アプリケーション名を指定します。 アプリケーション名は、Azure Multi-factor Authentication レポートに表示され、SMS またはモバイル アプリの認証メッセージに表示される可能性があります。
5. 調整アイドル タイムアウトと最大セッション時間既定値が十分でない場合。
6. チェック、**を必要と Multi-factor Authentication ユーザーの一致**場合、すべてのユーザーがされているか、および multi-factor authentication の対象サーバーにインポートされます。 サーバーにまだインポートされていないユーザーや、多要素認証から除外されるユーザーの数が多い場合、ボックスをオフのままにします。
7. チェック、 **Cookie キャッシュ**必要な場合です。
8. **[OK]**をクリックします。

## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Azure の Multi-factor Authentication Server の IIS プラグインを有効にします。
フォーム ベースの構成後または HTTP 認証の Url と設定、IIS で Azure Multi-factor Authentication IIS プラグインをロードされ有効にする必要がありますの場所を選択します。 次の手順を実行します。

1. IIS 6 でを実行する場合にをクリックして、 **ISAPI**タブです。 Web アプリケーションは、そのサイトの Azure Multi-factor Authentication ISAPI フィルター プラグインを有効にする (例: 既定の Web サイトの下) を実行している web サイトを選択します。
2. IIS 7 以降を実行している場合にクリックして、**ネイティブ モジュール**タブです。 サーバー、web サイト、または目的のレベルで IIS プラグインを有効にするアプリケーションを選択します。
3. クリックして、**を有効にする IIS 認証**画面の上部にあるボックスです。 Azure Multi-factor Authentication は、選択した IIS アプリケーション セキュリティ保護するようになりました。 ユーザーがサーバーにインポートされていることを確認します。

## <a name="trusted-ips"></a>信頼済み Ip
信頼済み Ip では、特定の IP アドレスまたはサブネットからの web サイト要求の Azure Multi-factor Authentication をバイパスすることができます。 たとえば、する可能性がありますユーザーを除外する Azure Multi-factor Authentication からログインしている場合、オフィスからです。 この場合は、信頼済み Ip のエントリとしてオフィスのサブネットを指定します。 信頼済み Ip を構成するのには、次の手順を使用します。

1. IIS 認証セクションで、をクリックして、**信頼済み Ip**タブです。
2. **[追加]**をクリックします。
3. 信頼済み Ip の追加 ダイアログ ボックスが表示されたら、選択、**単一の IP**、 **IP 範囲**、または**サブネット**ラジオ ボタンをクリックします。
4. IP アドレス、IP アドレスの範囲またはホワイト リストに登録する必要のあるサブネットを入力します。 サブネットを入力する場合、適切なネットマスクを選択し、クリックして**OK**です。 ホワイト リストが追加されました。

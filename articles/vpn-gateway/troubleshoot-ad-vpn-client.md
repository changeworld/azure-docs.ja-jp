---
title: 'VPN Gateway: VPN クライアントのトラブルシューティング - Azure AD 認証'
description: VPN Gateway P2S Azure AD 認証クライアントのトラブルシューティング
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 8871e92f0911c4d3cbcc1772bef1daeb5c70b5d7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151974"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Azure AD 認証 VPN クライアントのトラブルシューティング

この記事では、VPN クライアントがポイント対サイト VPN と Azure Active Directory 認証を使用して仮想ネットワークに接続する際のトラブルシューティングについて説明します。

## <a name="status"></a>状態ログを表示する

エラー メッセージの状態ログを表示します。

![logs](./media/troubleshoot-ad-vpn-client/1.png)

1. クライアント ウィンドウの右下隅にある矢印アイコンをクリックすると、**状態ログ**が表示されます。
2. 問題を示す可能性があるエラーについては、ログを確認してください。
3. エラー メッセージが赤色で表示されます。

## <a name="clear"></a>サインイン情報を消去する

サインイン情報を消去します。

![sign in](./media/troubleshoot-ad-vpn-client/2.png)

1. トラブルシューティングする プロファイルの横にある […] を選択します。 **[構成] -> [保存されたアカウントのクリア]** の順に選択します。
2. **[保存]** を選択します。
3. 接続を試みます。
4. 接続がまだ失敗する場合は、次のセクションに進みます。

## <a name="diagnostics"></a>診断を実行する

VPN クライアントで診断を実行します。

![診断](./media/troubleshoot-ad-vpn-client/3.png)

1. **[スキーマ]** プロパティで 診断を実行するプロファイルの横にある […] をクリックします。 **[診断] -> [Run Diagnosis]\(診断の実行\)** の順に選択します。
2. クライアントが一連のテストを実行し、テストの結果を表示します

   * インターネットへのアクセス – クライアントがインターネットに接続されているかどうかを確認します
   * クライアント資格情報 – Azure Active Directory 認証エンドポイントにアクセスできるかどうかを確認します
   * サーバーの解決 – DNS サーバーに接続して、構成されている VPN サーバーの IP アドレスを解決します
   * サーバーへのアクセス – VPN サーバーが応答しているかどうかを確認します
3. テストのいずれかが失敗した場合は、ネットワーク管理者に問い合わせて問題を解決してください。
4. 次のセクションでは、必要に応じてログを収集する方法について説明します。

## <a name="logfiles"></a>クライアントのログ ファイルを収集する

VPN クライアントのログ ファイルを収集します。 ログ ファイルは、選択した方法を使用して、サポート/管理者に送信できます。 たとえば、電子メールです。

1. 診断を 実行するプロファイルの横にある […] をクリックします。 **[診断] -> [ログ ディレクトリの表示]** の順に選択します。

   ![ログの表示](./media/troubleshoot-ad-vpn-client/4.png)
2. Windows Explorer が開いて、ログ ファイルが含まれているフォルダーに移動します。

   ![ファイルの表示](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>次の手順

詳細については、[Azure AD 認証を使用する P2S Open VPN 接続用の Azure Active Directory テナントの作成](openvpn-azure-ad-tenant.md)に関するページを参照してください。
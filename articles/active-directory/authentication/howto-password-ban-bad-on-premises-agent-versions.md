---
title: オンプレミスの Azure AD パスワード保護エージェントのバージョンのリリース履歴
description: バージョンのリリースと動作の変更履歴に関するドキュメント
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 11/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 89d64a28d2fe43464995e434c9f3807047b29492
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913638"
---
# <a name="preview--azure-ad-password-protection-agent-version-history"></a>プレビュー: Azure AD パスワード保護エージェントのバージョン履歴

|     |
| --- |
| Azure AD パスワード保護は、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

## <a name="12250"></a>1.2.25.0

リリース日: 2018 年 11 月 1 日

修正

* DC エージェントとプロキシ サービスは、証明書の信頼の問題により失敗することがなくなりました。
* DC エージェントとプロキシ サービスには、FIPS 準拠マシンに関する追加の修正があります。
* プロキシ サービスは、TLS 1.2 のみのネットワーク環境で正しく動作するようになりました。
* パフォーマンスと信頼性のマイナー修正
* ログ記録の強化

変更点:

* プロキシ サービスに必要な最低の OS レベルが、Windows Server 2012 R2 になりました。 DC エージェント サービスに必要な最低の OS レベルは、Windows Server 2012 のままです。
* パスワード検証アルゴリズムでは、拡張文字正規化テーブルが使用されます。 これにより、以前のバージョンでは受け入れられていたパスワードが拒否される可能性があります。

## <a name="12100"></a>1.2.10.0

リリース日: 2018 年 8 月 17 日

修正

* Register-AzureADPasswordProtectionProxy と Register-AzureADPasswordProtectionForest では、多要素認証がサポートされるようになりました。
* Register-AzureADPasswordProtectionProxy では、暗号化エラーを回避するために、ドメインに WS2012 以降のドメイン コントローラーが必要です。
* DC エージェント サービスを使用すると、起動時の Azure からの新しいパスワード ポリシーの要求に関する信頼性が高くなります。
* DC エージェント サービスでは、必要に応じて 1 時間ごとに Azure から新しいパスワード ポリシーが要求されますが、ランダムに選択された開始時刻に要求されるようになります。
* DC エージェント サービスを使用すると、レプリカとしてプロモーションされる前にサーバー上にインストールされるときに、新しい DC アドバタイズで無限に遅延が発生することがなくなります。
* DC エージェント サービスでは、[Windows Server Active Directory のパスワード保護を有効にする] の構成設定を優先します。
* 今後のバージョンにアップグレードするときに、DC エージェントとプロキシ インストーラーの両方で一括アップグレードがサポートされるようになります。

> [!WARNING]
> バージョン 1.1.10.3 からの一括アップグレードはサポートされておらず、インストール エラーが発生します。 バージョン 1.2.10 以降にアップグレードするには、まず DC エージェントとプロキシ サービス ソフトウェアを完全にアンインストールし、最初から新しいバージョンをインストールする必要があります。 Azure AD パスワード保護プロキシ サービスの再登録が必要です。  フォレストを再登録する必要はありません。

> [!NOTE]
> DC エージェント ソフトウェアの一括アップグレードには再起動が必要です。

* DC エージェントとプロキシ サービスでは、FIPS 準拠アルゴリズムのみを使用するように構成されたサーバーでの実行をサポートします。
* パフォーマンスと信頼性のマイナー修正
* ログ記録の強化

## <a name="11103"></a>1.1.10.3

リリース日: 2018 年 6 月 15 日

初期パブリック プレビュー リリース

## <a name="next-steps"></a>次の手順

[Azure AD のパスワード保護をデプロイする](howto-password-ban-bad-on-premises-deploy.md)

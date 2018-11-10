---
title: オンプレミスの Azure AD パスワード保護エージェントのバージョンのリリース履歴
description: バージョンのリリースと動作の変更履歴に関するドキュメント
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: c52c84a1311c30c19356bb8a1287b203faf476fc
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/01/2018
ms.locfileid: "50742907"
---
# <a name="preview--azure-ad-password-protection-agent-version-history"></a>プレビュー: Azure AD パスワード保護エージェントのバージョン履歴

|     |
| --- |
| Azure AD パスワード保護は、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

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
* ログ記録の強化
* パフォーマンスと信頼性のマイナー修正

## <a name="11103"></a>1.1.10.3

リリース日: 2018 年 6 月 15 日

初期パブリック プレビュー リリース

## <a name="next-steps"></a>次の手順

[Azure AD のパスワード保護をデプロイする](howto-password-ban-bad-on-premises-deploy.md)

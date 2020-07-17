---
title: ページ レイアウト バージョン
titleSuffix: Azure AD B2C
description: カスタム ポリシーでの UI カスタマイズのページ レイアウトのバージョン履歴。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3d0cb06f84fdd96d099e05f55ba62c37cb1192c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183977"
---
# <a name="page-layout-versions"></a>ページ レイアウト バージョン

ページ レイアウト パッケージは定期的に更新され、ページ要素に修正と機能強化が加えられます。 次の変更ログは、各バージョンで導入された変更を示しています。

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="200"></a>2.0.0

- セルフアサート ページ (`selfasserted`)
  - カスタム ポリシーでの[表示コントロール](display-controls.md)のサポートが追加されました。

## <a name="120"></a>1.2.0

- すべてのページ
  - アクセシビリティの修正
  - [HTML タグに](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content)`data-preload="true"` 属性を追加して、CSS と JavaScript の読み込み順序を制御できるようになりました。
    - HTML テンプレートと同時にリンクされた CSS ファイルを読み込みます。これにより、ファイルの読み込みの間に "ちらつき" が発生しなくなります。
    - ページの読み込み前に `script` タグをフェッチして実行する順序を制御します。
  - メール フィールドは `type=email` になり、モバイル キーボードで正しい候補が提供されるようになりました
  - Chrome 翻訳のサポート
- 統合されたセルフアサート ページ
  - ユーザー名やメールとパスワードのフィールドには `form` HTML 要素が使用され、Microsoft Edge および Internet Explorer (IE) でこの情報を適切に保存できるようになりました。

## <a name="110"></a>1.1.0

- 例外ページ (globalexception)
  - アクセシビリティの修正
  - 連絡先がない場合のポリシーからの既定のメッセージが削除されました
  - 既定の CSS が削除されました
- MFA ページ (多要素)
  - [Confirm Code]\(コードの確認\) ボタンが削除されました
  - コードの入力フィールドは、最大 6 文字の入力のみを受け取るようになりました
  - 6 桁のコードを入力すると、入力したコードの確認がページによって自動的に試行されます。ボタンをクリックする必要はありません
  - コードが正しくない場合、入力フィールドは自動的にクリアされます
  - 正しくないコードが 3 回試行されると、B2C から証明書利用者にエラーが送信されます
  - アクセシビリティの修正
  - 既定の CSS が削除されました
- セルフアサート ページ (selfasserted)
  - キャンセル アラートが削除されました
  - エラー要素の CSS クラス
  - エラーの表示/非表示ロジックが改善されました
  - 既定の CSS が削除されました
- SSP が統一されました (unifiedssp)
  - サインインしたままにする (KMSI) コントロールが追加されました

## <a name="100"></a>1.0.0

- 最初のリリース

## <a name="next-steps"></a>次のステップ

カスタム ポリシーでアプリケーションのユーザー インターフェイスをカスタマイズする方法の詳細については、「[カスタム ポリシーを使用してアプリケーションのユーザー インターフェイスをカスタマイズする](custom-policy-ui-customization.md)」を参照してください。

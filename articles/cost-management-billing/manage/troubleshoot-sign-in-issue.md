---
title: Azure サブスクリプションのサインインに関する問題のトラブルシューティング
description: Azure portal にサインインできないという問題を解決するのに役立ちます。
services: cost-management-billing
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 07/16/2021
ms.author: banders
ms.openlocfilehash: 7fad89f2ef4898e3fb790860abbb56593e9d06f1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423130"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Azure サブスクリプションのサインインに関する問題のトラブルシューティング

このガイドは、Azure portal にサインインできないという問題を解決するのに役立ちます。

> [!NOTE]
> 新しい Azure アカウントへのサインアップで問題が発生した場合は、[Azure サブスクリプションのサインインの問題のトラブルシューティング](./troubleshoot-azure-sign-up.md)に関するページを参照してください。

## <a name="page-hangs-in-the-loading-status"></a>ページの読み込み中にハングする

インターネット ブラウザーのページがハングする場合、Azure portal にアクセスできるまで、次の各手順を試してみてください。

- ページを更新します。
- 別のインターネット ブラウザーを使用します。
- お使いのブラウザーで、プライベート ブラウズ モードを使用します。

   - **Edge の場合:** **設定** (ご自分のプロファイル画像近くの 3 つのドット) を開き、 **[新しい InPrivate ウィンドウ]** を選択し、[Azure portal](https://portal.azure.com/) に移動してサインインします。 
   - **Chrome の場合:** **Incognito** モードを選択します。
   - **Safari の場合:** **[ファイル]** を選択し、 **[新規プライベート ウィンドウ]** を選択します。

- 次のように、キャッシュをクリアし、インターネット Cookie を削除します。

   - **Edge の場合:** **[設定]** を開き、 **[プライバシーとサービス]** を選択します。 **[閲覧データをクリア]** の手順に従います。 **[閲覧履歴]** 、 **[ダウンロード履歴]** 、および **[キャッシュされた画像とファイル]** のチェックボックスが選択されていることを確認し、 **[削除]** を選択します。
   - **Chrome の場合:** **[設定]** を選択し、 **[プライバシーとセキュリティ]** の **[閲覧データのクリア]** を選択します。

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>サインイン時に自動で別のユーザーになる

この問題は、インターネット ブラウザーで複数のユーザー アカウントを使用している場合に発生する可能性があります。

この問題を解決するには、次の方法のいずれかを試してください。

- キャッシュをクリアし、インターネット Cookie を削除する。

   - **Edge の場合:** **[設定]** を開き、 **[プライバシーとサービス]** を選択します。 **[閲覧データをクリア]** の手順に従います。 **[閲覧履歴]** 、 **[ダウンロード履歴]** 、 **[クッキー]** および **[キャッシュされた画像とファイル]** のチェックボックスが選択されていることを確認し、 **[削除]** を選択します。
   - **Chrome の場合:** **[設定]** を選択し、 **[プライバシーとセキュリティ]** の **[閲覧データのクリア]** を選択します。
- お使いのブラウザーの設定を既定にリセットします。
- お使いのブラウザーのプライベート ブラウズ モードを使用します。 
   - **Edge の場合:** **設定** (ご自分のプロファイル画像近くの 3 つのドット) を開き、 **[新しい InPrivate ウィンドウ]** を選択し、[Azure portal](https://portal.azure.com/) に移動してサインインします。 
   - **Chrome の場合:** **Incognito** モードを選択します。
   - **Safari の場合:** **[ファイル]** を選択し、 **[新規プライベート ウィンドウ]** を選択します。

## <a name="i-can-sign-in-but-i-see-the-error-no-subscriptions-found"></a>サインインすることはできるが、サブスクリプション見つかりませんでした、というエラーが表示される

この問題は、誤ったディレクトリで選択した場合、または、自分のアカウントに十分なアクセス許可がない場合に発生します。

**シナリオ:** [Azure portal](https://portal.azure.com/) へのサインインでエラーが発生した

この問題を解決するには、次の手順に従います。

- 右上隅にある自分のアカウントをクリックして、適切な Azure ディレクトリが選択されていることを確認します。
- 正しい Azure ディレクトリが選択されていてもエラー メッセージが表示される場合は、ご自分のアカウントを[所有者として追加](./add-change-subscription-administrator.md)します。

## <a name="additional-help-resources"></a>その他のヘルプ リソース

Azure の請求とサブスクリプションに関するその他のトラブルシューティング記事

- [拒否されたカード](./troubleshoot-declined-card.md)
- [サブスクリプションのサインアップの問題](./troubleshoot-azure-sign-up.md)
- [サブスクリプションが見つからない](./no-subscriptions-found.md)
- [Enterprise コスト ビューが無効になっている](./enterprise-mgmt-grp-troubleshoot-cost-view.md)
- [Azure の課金に関するドキュメント](../index.yml)

## <a name="contact-us-for-help"></a>お問い合わせ

質問があるか、またはサポートを必要としているが、Azure portal にサインインできない場合は、[サポート リクエストを作成](https://support.microsoft.com/oas/?prid=15470)してください。
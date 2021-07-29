---
title: Azure Key Vault 内の認証シークレットの保護
description: マネージド ID を使用して、Azure Key Vault 内の認証シークレットをセキュリティで保護します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/17/2021
ms.author: cshoe
ms.openlocfilehash: cc0ced1a6c91bf2e7960e638c295d33a45db135e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110073344"
---
# <a name="securing-authentication-secrets-in-azure-key-vault"></a>Azure Key Vault 内の認証シークレットの保護

カスタム認証プロバイダーを構成する際に、Azure Key Vault に接続シークレットを格納することが望ましく思える場合があります。 この記事では、カスタム認証シークレットを取得するため、マネージド ID を使用して、Azure Static Web Apps に Key Vault へのアクセスを付与する方法を説明します。

セキュリティ シークレットを使用するには、次の項目を整える必要があります。

- Static Web Apps インスタンスでシステム割り当て ID を作成する。
- その ID に Key Vault シークレットへのアクセス権を付与する。
- Static Web Apps のアプリケーション設定から、その Key Vault シークレットを参照する。

この記事では、お客様のアプリケーションでこれらの各項目を設定する方法について説明します。

## <a name="prerequisites"></a>前提条件

- 既存の Azure Static Web Apps サイト。
- シークレット値が入った既存の Key Vault リソース。

## <a name="create-identity"></a>ID の作成

1. Azure portal で Static Web Apps サイトを開きます。

1. _[設定]_ メニューから、 **[ID]** を選択します。

1. **[システム割り当て済み]** タブを選択します。

1. _[状態]_ ラベルの **[オン]** ボタンを選択します。

1. **[保存]** を選択します。

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-enable-managed-identity.png" alt-text="システム割り当て ID の追加":::

1. 確認のダイアログが表示されたら、 **[はい]** ボタンを選択します。

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-enable-managed-identity-confirmation.png" alt-text="ID 割り当てを確認する。":::

これで、静的 Web アプリで Key Vault シークレットを読み取るためのアクセス ポリシーを追加できるようになりました。

## <a name="add-a-key-vault-access-policy"></a>Key Vault アクセス ポリシーを追加する

1. Azure portal で、ご使用の Key Vault リソースを開きます。

1. _[設定]_ メニューから、 **[アクセス ポリシー]** を選択します。

1. **[アクセス ポリシーの追加]** リンクを選択します。

1. _[シークレットのアクセス許可]_ ドロップダウンから、 **[取得]** を選択します。

1. _[プリンシパルの選択]_ ラベルの横にある **[選択されていません]** リンクを選択します。

1. 検索ボックスで、お客様の Static Web Apps アプリケーション名を検索します。

1. アプリケーション名に一致する一覧の項目を選択します。

1. **[選択]** ボタンを選択します。

1. **[追加]** ボタンを選びます。

1. **[保存]** を選択します。

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-key-vault-save-policy.png" alt-text="Key Vault アクセス ポリシーを保存する":::

これで、アクセス ポリシーが Key Vault に保存されます。 次に、静的 Web アプリを Key Vault リソースに関連付けるときに使用するシークレットの URI にアクセスします。

1. _[設定]_ メニューから、 **[シークレット]** を選択します。

1. 一覧から目的のシークレットを選択します。

1. 一覧から目的のシークレットのバージョンを選択します。

1. _[シークレット識別子]_ テキスト ボックスの末尾にある **コピー ボタン** を選択して、シークレットの URI 値をクリップボードにコピーします。

1. 後で使用できるように、この値をテキスト エディターに貼り付けます。

## <a name="add-application-setting"></a>アプリケーション設定を追加する

1. Azure portal で Static Web Apps サイトを開きます。

1. _[設定]_ メニューから、 **[構成]** を選択します。

1. _[アプリケーション設定]_ セクションから、 **[追加]** ボタンを選択します。

1. "_名前_" フィールドのテキスト ボックスに名前を入力します。

1. "_値_" フィールドのテキスト ボックスでシークレット値を決定します。

    このシークレット値は、いくつかの異なる値を組み合わせたものです。 最終的にどのような文字列になるかを次のテンプレートに示します。

    ```text
    @Microsoft.KeyVault(SecretUri=<YOUR-KEY-VAULT-SECRET-URI>)
    ```

    次の手順に従って、完全なシークレット値を作成します。

1. 上のテンプレートをコピーして、テキスト エディターに貼り付けます。

1. `<YOUR-KEY-VAULT-SECRET-URI>` を、前に準備しておいた Key Vault URI 値に置き換えます。

1. 新しい完全な文字列値をコピーします。

1. この値を、"_値_" フィールドのテキスト ボックスに貼り付けます。

1. **[OK]** ボタンを選択します。

1. _[アプリケーション設定]_ ツール バーの上部にある **[保存]** ボタンを選択します。

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-application-settings-save.png" alt-text="アプリケーション設定を保存する":::

これで、新しく作成されたアプリケーション設定がカスタム認証構成から参照されるときに、お客様の静的 Web アプリの ID を使用して Azure Key Vault からその値が抽出されることになります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [カスタム認証](./authentication-custom.md)

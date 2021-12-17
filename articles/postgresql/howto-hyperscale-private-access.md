---
title: プライベート アクセス (プレビュー) を有効にする - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL - Hyperscale (Citus) 用のサーバー グループでプライベート リンクを設定する方法
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/15/2021
ms.openlocfilehash: e7694cd55eb6b6da7adb0313de47575880854cc5
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074929"
---
# <a name="private-access-preview-in-azure-database-for-postgresql-hyperscale-citus"></a>Azure Database for PostgreSQL Hyperscale (Citus) でのプライベート アクセス (プレビュー)

[プライベート アクセス](concepts-hyperscale-private-access.md) (プレビュー) を使用すると、Azure 仮想ネットワーク内のリソースから、Hyperscale (Citus) サーバー グループ内のノードに安全かつプライベートに接続できます。 この記事では、仮想ネットワークとサブネットが既に作成されているものとします。 前提条件を設定する例については、[プライベート アクセスのチュートリアル](tutorial-hyperscale-private-access.md)を参照してください。

## <a name="create-a-server-group-with-a-private-endpoint"></a>プライベート エンドポイントを使用してサーバー グループを作成する

1. Azure Portal の左上隅にある **[リソースの作成]** を選択します。

2. **[新規]** ページで **[データベース]** を選択し、 **[データベース]** ページで **[Azure Database for PostgreSQL]** を選択します。

3. デプロイ オプションについては、**[Hyperscale (Citus) サーバー グループ]** の下にある **[作成]** ボタンを選択します。

4. 新しいサーバーの詳細フォームで、リソース グループ、目的のサーバー グループ名、場所、データベース ユーザーのパスワードを指定します。

5. **[サーバー グループの構成]** を選択し、使用するプランを選択して、**[保存]** を選択します。

6. **ページの下部にある [Next: Networking]\(次へ: ネットワーク\)** を選択します。

7. **[プライベート アクセス (プレビュー)]** を選択します。

    > [!NOTE]
    >
    > プライベート アクセスは、[特定のリージョン](concepts-hyperscale-limits.md#regions)でのみプレビューで使用できます。
    >
    > サーバー グループが許可されたリージョン内にあるにもかかわらず、サーバー グループに対してプライベート アクセス オプションを選択できない場合は、Azure [サポート リクエスト](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を開き、Azure サブスクリプション ID を含めて、アクセス権を取得してください。

8. **[プライベート エンドポイントの作成]** という画面が表示されます。 既存リソースの適切な値を選択して、**[OK]** をクリックします。

    - **リソース グループ**
    - **場所**
    - **名前**
    - **ターゲット サブリソース**
    - **Virtual Network**
    - **サブネット**
    - **プライベート DNS ゾーンとの統合**

9. プライベート エンドポイントを作成したら、**[確認と作成]** を選択して、Hyperscale (Citus) サーバー グループを作成します。

## <a name="enable-private-access-on-an-existing-server-group"></a>既存のサーバー グループでプライベート アクセスを有効にする

既存のサーバー グループのノードにプライベート エンドポイントを作成するには、サーバー グループの **[ネットワーク]** ページを開きます。

1. **[+ プライベート エンドポイントの追加]** を選択します。

   :::image type="content" source="media/howto-hyperscale-private-access/networking.png" alt-text="[ネットワーク] 画面":::

1. **[基本]** タブで、**[サブスクリプション]**、**[リソース グループ]**、**[リージョン]** を確認します。 エンドポイントの **[名前]** を入力します (`my-server-group-eq` など)。

    > [!NOTE]
    >
    > 他を選択する妥当な理由がない限り、サーバー グループのものと一致するサブスクリプションとリージョンを選択することをお勧めします。  フォームのフィールドの既定値は正しくない可能性があります。確認し、必要であれば更新します。

2. **[次へ: リソース >]** を選択します。 **[対象サブリソース]** で、サーバー グループのターゲット ノードを選択します。 通常は、`coordinator` が必要なノードです。

3. **Next:構成 >** をクリックします。 適切な **[仮想ネットワーク]** と **[サブネット]** を選択します。 **[プライベート DNS 統合]** をカスタマイズするか、既定の設定をそのまま使用します。

4. **[次へ: タグ >]** を選択して、必要なタグを追加します。

5. 最後に、**[確認および作成 >]** を選択します。 設定を確認し、問題がなければ **[作成]** を選択します。

## <a name="next-steps"></a>次のステップ

* [プライベート アクセス](concepts-hyperscale-private-access.md) (プレビュー) の詳細を学習する。
* [チュートリアル](tutorial-hyperscale-private-access.md)に従って、プライベート アクセス (プレビュー) の動作を確認する。

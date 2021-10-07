---
title: Azure Purview Studio を使用する
description: この記事では、Azure Purview Studio の使用方法について説明します。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 2e3bcd7e51226f437f42f03fa43b144bee434f33
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456140"
---
# <a name="use-purview-studio"></a>Purview Studio の使用

この記事では、Azure Purview のいくつかの主な機能の概要について説明します。

## <a name="prerequisites"></a>前提条件

* Azure Purview アカウントが Azure portal で既に作成されており、ユーザーが [Purview Studio](https://web.purview.azure.com/resource/) にアクセスするためのアクセス許可を持っていること。

## <a name="launch-purview-account"></a>Purview アカウントの起動

* Purview アカウントを起動するには、Azure portal の [Purview アカウント] に移動し、起動するアカウントを選択して、そのアカウントを起動します。

  :::image type="content" source="./media/use-purview-studio/open-purview-studio.png" alt-text="Purview Studio ボタンが強調表示された、Azure portal の Purview ウィンドウのスクリーンショット。" border="true":::

* Purview アカウントを起動する別の方法として、`https://web.purview.azure.com` にアクセスし、 **[Azure Active Directory]** とアカウント名を選択してアカウントを起動することもできします。

## <a name="home-page"></a>ホーム ページ

**ホーム** は、Azure Purview クライアントの開始ページです。

:::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="ホームページのスクリーンショット。":::

次の一覧は、**ホーム ページ** の主な機能をまとめたものです。 一覧にある各番号は、前のスクリーンショットで強調表示されている番号に対応しています。

1. カタログのフレンドリ名。 カタログ名は、 **[管理]**  >  **[アカウント情報]** で設定できます。

2. カタログ分析には、以下の数が表示されます。

   * データ ソース
   * アセット
   * 用語集の用語

3. 検索ボックスを使用すると、データ カタログ全体でデータ資産を検索できます。

4. クイック アクセス ボタンを使用すると、アプリケーションのよく使用される機能にアクセスできます。 表示されるボタンは、ルート コレクションにあるユーザー アカウントに割り当てられているロールによって異なります。

   * "*コレクション管理者*" の場合、使用可能なボタンは **[ナレッジ センター]** です。
   * "*データ キュレーター*" の場合、ボタンは **[資産の参照]** 、 **[用語集の管理]** 、 **[ナレッジ センター]** です。
   * "*データ閲覧者*" の場合、ボタンは **[資産の参照]** 、 **[用語集の表示]** 、 **[ナレッジ センター]** です。
   * "*データ ソース管理者* + *データ キュレーター*" の場合、ボタンは **[資産の参照]** 、 **[用語集の管理]** 、 **[ナレッジ センター]** です。
   * "*データ ソース管理者* + *データ閲覧者*" の場合、ボタンは **[資産の参照]** 、 **[用語集の表示]** 、 **[ナレッジ センター]** です。
  
   > [!NOTE]
   > Azure Purview ロールの詳細については「[Azure Purview でのアクセスの制御](catalog-permissions.md)」を参照してください。

5. 左側のナビゲーション バーは、アプリケーションのメイン ページを見つける場合に役立ちます。   
6. **[最近アクセス]** タブには、最近アクセスしたデータ資産の一覧が表示されます。 資産へのアクセス方法について詳しくは、[Data Catalog の検索](how-to-search-catalog.md)および[資産の種類での参照](how-to-browse-catalog.md)に関する記事をご覧ください。  **[マイ アイテム]** タブは、ログオンしているユーザーが所有するデータ資産の一覧です。
7. **[リンク]** には、リージョンの状態、ドキュメント、価格、概要、Purview の状態へのリンクが含まれています
8. 上部のナビゲーション バーには、リリース ノートや更新プログラム、Purview アカウントの変更、通知、ヘルプ、フィードバックの各セクションに関する情報が含まれています。

## <a name="knowledge-center"></a>ナレッジ センター

ナレッジ センターは、Purview に関連するすべてのビデオとチュートリアルを検索できる場所です。

## <a name="guided-tours"></a>ガイド ツアー

Azure Purview Studio の各 UX には、ページの概要を示すガイド ツアーが用意されています。 ガイド ツアーを開始するには、上部のバーにある **[ヘルプ]** を選択して、 **[ガイド ツアー]** を選択します。

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="ガイド ツアーのスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [セキュリティ プリンシパルを追加する](tutorial-scan-data.md)

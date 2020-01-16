---
title: アプリを最新のスキーマに移行する
description: ロジック アプリ ワークフローの JSON 定義を最新のワークフロー定義言語スキーマ バージョンに移行する方法
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: cef0fcb990cd2c5c6583822d4dc4c6993c52eac2
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666790"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>ロジック アプリを最新のスキーマ バージョンに移行する

既存のロジック アプリを最新のスキーマに移行するには、次の手順に従います。 

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

2. ロジック アプリのメニューで、 **[概要]** を選択します。 ツール バーで **[スキーマの更新]** を選択します。

   > [!NOTE]
   > **[スキーマの更新]** を選択すると、Azure Logic Apps によって自動的に移行ステップが実行され、必要な出力コードが提供されます。 この出力を使用してロジック アプリの定義を更新することができます。 ただし、以降の「**ベスト プラクティス**」セクションに記載されているベスト プラクティスには必ず従ってください。

   ![スキーマの更新](./media/connectors-schema-migration/update-schema.png)

   [スキーマの更新] ページが表示され、新しいスキーマの改良点について書かれたドキュメントへのリンクが表示されます。

## <a name="best-practices"></a>ベスト プラクティス

以下に、ロジック アプリを最新のスキーマ バージョンに移行するうえでのベスト プラクティスをいくつか紹介します。

* 移行したスクリプトを新しいロジック アプリにコピーします。 テストが完了して、移行後のアプリが正しく動作することを確認するまでは、以前のバージョンを上書きしないでください。

* 運用環境に展開する **前に** ロジック アプリをテストします。

* 移行が完了したら、できるだけ [マネージド API](../connectors/apis-list.md) を使用するようにロジック アプリを更新します。 たとえば、DropBox v1 が使われている箇所はすべて Dropbox v2 を使用するように変更します。

## <a name="next-steps"></a>次のステップ

* [ロジック アプリを手動で移行する方法について](../logic-apps/logic-apps-schema-2015-08-01.md)

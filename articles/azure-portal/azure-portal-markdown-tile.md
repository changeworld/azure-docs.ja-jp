---
title: Azure ダッシュボードでカスタムのマークダウン タイルを使用する
description: マークダウン タイルを Azure ダッシュボードに追加して、静的コンテンツを表示する方法について説明します
ms.date: 03/19/2021
ms.topic: how-to
ms.custom: devx-track-js
ms.openlocfilehash: 8324b736565cfa353e48cf49b76e2784866f47f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774458"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Azure ダッシュボードでマークダウン タイルを使用して、カスタム コンテンツを表示する

マークダウン タイルを Azure ダッシュボードに追加して、カスタム コンテンツ、静的コンテンツを表示できます。 たとえば、マークダウン タイルで、基本的な手順、画像、または一連のハイパーリンクを表示できます。

## <a name="add-a-markdown-tile-to-your-dashboard"></a>マークダウン タイルをダッシュボードに追加する

1. Azure portal のサイドバーから **[ダッシュボード]** を選択します。

   ![ポータルのサイドバーを示すスクリーンショット](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. [ダッシュボード] ビューで、カスタムのマークダウン タイルが表示されるはずのダッシュボードを選択し、 **[編集]** を選択します。

   ![ダッシュボード編集ビューを示すスクリーンショット](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. **[タイル ギャラリー]** で **[マークダウン]** というタイルを見つけて、 **[追加]** を選択します。 タイルがダッシュボードに追加され、 **[マークダウンの編集]** ウィンドウが開きます。

1. **[タイトル]** と **[サブタイトル]** の値を入力します。これらは、別のフィールドに移動するとタイルに表示されます。

   ![タイトルとサブタイトルの入力結果を示すスクリーンショット](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. マークダウン コンテンツを含めるオプションとして、 **[インライン編集]** または **[URL を使用してコンテンツを挿入する]** のいずれかを選択します。

   - マークダウンを直接入力する場合は、 **[インライン編集]** を選択します。

      ![インライン コンテンツの入力を示すスクリーンショット](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - オンラインでホストされている既存のマークダウン コンテンツを使用する場合は、 **[URL を使用してコンテンツを挿入する]** を選択します。

      ![URL の入力を示すスクリーンショット](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > セキュリティを強化するために、マークダウン ファイルを作成して[暗号化が有効になっている Azure ストレージ アカウント BLOB](../storage/common/storage-service-encryption.md) に保存し、URL オプションを使用してそのファイルを指定できます。 マークダウン コンテンツは、ストレージ アカウントの暗号化オプションを使用して暗号化されます。 そのファイルに対するアクセス許可を持つユーザーのみが、ダッシュボードでマークダウン コンテンツを表示できます。 Azure portal ( _https://portal.azure.com/_ ) で BLOB のマークダウン ファイルにアクセスできるよう、ストレージ アカウントに [クロスオリジン リソース共有 (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) ルールを設定することが必要な場合があります。

1. **[完了]** を選択して、**[マークダウンの編集]** ウィンドウを閉じます。 コンテンツが [マークダウン] タイルに表示されます。これは、右下隅のハンドルをドラッグしてサイズ変更できます。

   ![カスタムのマークダウン タイルを示すスクリーンショット](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>マークダウン コンテンツの機能と制限

マークダウン タイルでは、プレーン テキスト、マークダウン構文、HTML コンテンツの任意の組み合わせを使用できます。 Azure portal により "_マーク済_" というオープンソース ライブラリが使用され、コンテンツがタイルに表示される HTML に変換されます。 "_マーク済_" によって生成される HTML が、レンダリングされる前にポータルによって事前処理されます。 この手順は、独自のカスタマイズが、セキュリティまたはポータルのレイアウトに影響しないことを確認するのに役立ちます。 事前処理中に、潜在的な脅威となる HTML のすべての部分が削除されます。 次の種類のコンテンツは、ポータルによって許可されていません。

* JavaScript – `<script>` タグとインライン JavaScript 評価が削除されます。
* iframe - `<iframe>` タグは削除されます。
* スタイル - `<style>` タグは削除されます。 HTML 要素のインライン スタイル属性は、公式にはサポートされていません。 一部のインライン スタイル要素が動作する場合がありますが、ポータルのレイアウトに干渉した場合、いつでもその動作は停止します。 [マークダウン] タイルは、ポータルの既定のスタイルを使用する基本的な静的コンテンツを対象としています。

## <a name="next-steps"></a>次の手順

* カスタム ダッシュボードを作成するには、「[Azure portal でのダッシュボードの作成および共有方法](../azure-portal/azure-portal-dashboards.md)」を参照してください。

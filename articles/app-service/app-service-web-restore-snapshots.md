---
title: アプリをスナップショットから復元する
description: スナップショットからアプリを復元する方法について説明します。 自動シャドウ コピーを使用して、Premium レベルの予期しないデータ損失から回復します。
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.topic: article
ms.date: 09/02/2021
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: 48b3d859a73a2d63644e1d5881c3505cee93f9d7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679505"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Azure でスナップショットからアプリケーションを復元する
この記事では、[Azure App Service](../app-service/overview.md) でスナップショットからアプリを復元する方法について説明します。 アプリのスナップショットの 1 つに基づいて、アプリを以前の状態に復元することができます。 スナップショットを有効にする必要はありません。データの復旧の目的で、すべてのアプリのスナップショットがプラットフォームによって自動的に保存されます。

スナップショットは、App Service アプリの増分シャドウ コピーです。 そのアプリが Premium レベル以上である場合、App Service では、アプリのコンテンツとその構成の両方の定期的なスナップショットを取得します。 これらには、[標準のバックアップ](manage-backup.md)に比べて次のようないくつかの利点があります。

- ファイル ロックによるファイル コピーエラーがありません。
- 最大スナップショット サイズの増加 (30 GB)。
- サポートされている価格レベルでは構成が必要ありません。
- スナップショットは、どの Azure リージョンでも新しい App Service アプリに復元できます。

スナップショットからの復元は、**Premium** レベル以上で実行されているアプリで利用できます。 アプリのスケーリングについて詳しくは、「 [Azure App Service の Web アプリをスケーリングする](manage-scale-up.md)」をご覧ください。

## <a name="limitations"></a>制限事項

- スナップショット復元でサポートされる最大サイズは 30 GB です。 ストレージ サイズが 30 GB を超えている場合、スナップショット復元は失敗します。 ストレージ サイズを削減するには、ログ、画像、オーディオ、ビデオなどのファイルを、たとえば [Azure Storage](../storage/index.yml) に移動することを検討してください。
- [標準のバックアップ](manage-backup.md#what-gets-backed-up)でサポートされる接続されているデータベースや、[マウントされている Azure ストレージ](configure-connect-to-azure-storage.md?pivots=container-windows)はすべて、スナップショットに含まれ "*ません*"。 接続されている Azure サービスのネイティブ バックアップ機能 ([SQL Database](../azure-sql/database/automated-backups-overview.md) や [Azure Files](../storage/files/storage-snapshots-files.md) など) を使用すること検討してください。
- App Service は、スナップショットの復元中にターゲット アプリやターゲット スロットを停止します。 運用アプリのダウンタイムを最小限に抑えるには、スナップショットをまず[ステージング スロット](deploy-staging-slots.md)に復元してから、運用環境にスワップしてください。
- 過去 30 日間のスナップショットを使用できます。 保持期間やスナップショットの頻度は構成できません。
- App Service 環境で実行される App Services はスナップショットをサポートしていません。

## <a name="restore-an-app-from-a-snapshot"></a>アプリをスナップショットから復元する

1. [Azure Portal](https://portal.azure.com) にあるアプリの **[設定]** ページで、 **[バックアップ]** をクリックして **[バックアップ]** ページを表示します。 次に、 **[スナップショット (プレビュー)]** セクションにある **[復元]** をクリックします。
   
    ![スナップショットからアプリを復元する方法を示すスクリーンショット。](./media/app-service-web-restore-snapshots/1.png)

2. **[復元]** ページで、復元するスナップショットを選択します。
   
    ![復元するスナップショットを選択する方法を示すスクリーンショット。 ](./media/app-service-web-restore-snapshots/2.png)
   
3. **[復元先]** でアプリの復元先を指定します。
   
    ![復元先を指定する方法を示すスクリーンショット。](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > ベスト プラクティスとして、新しいスロットに復元してから、入れ替えを実行することをお勧めします。 **[上書き]** を選択した場合、アプリの現在のファイル システム内にある既存のデータはすべて消去され、上書きされます。 **[OK]** をクリックする前に、実行する操作内容を確認します。
   > 
   > 
      
   > [!Note]
   > 現在は技術的な制限があるため、同じスケール ユニット内のアプリにのみ復元することができます。 この制限は、今後のリリースで解消される予定です。
   > 
   > 
   
    **[既存のアプリ]** を選択してスロットに復元することができます。 このオプションを使用する前に、アプリにスロットを作成しておく必要があります。

4. サイト構成の復元を選択することができます。
   
    ![サイト構成を復元する方法を示すスクリーンショット。](./media/app-service-web-restore-snapshots/4.png)

5. **[OK]** をクリックします。

---
title: Azure Firewall で IP グループを作成する
description: IP グループを使用すると、Azure Firewall 規則の IP アドレスをグループ化して管理できます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 7e8b2350b9e85d07ce1c399ce4536703ec998cbc
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444856"
---
# <a name="create-ip-groups-preview"></a>IP グループを作成する (プレビュー)

> [!IMPORTANT]
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。

IP グループを使用すると、Azure Firewall 規則の IP アドレスをグループ化して管理できます。 1 つの IP アドレス、複数の IP アドレス、または 1 つ以上の IP アドレス範囲を設定できます。

## <a name="create-an-ip-group"></a>IP グループを作成する

1. Azure portal のホーム ページから **[リソースの作成]** を選択します。
2. 検索テキスト ボックスに「**IP グループ**」と入力し、 **[IP Groups]\(IP グループ\)** を選択します。
3. **作成** を選択します。
4. サブスクリプションを選択します。
5. リソース グループを選択するか、新しく作成します。
6. IP グループの一意の名前を入力し、リージョンを選択します。

6. **[次へ: IP アドレス]** を選択します。
7. 1 つの IP アドレス、複数の IP アドレス、または IP アドレス範囲を入力します。

   IP アドレスを入力するには、2 つの方法があります。
   - 手動で入力できます
   - ファイルからインポートできます

   ファイルからインポートするには、 **[ファイルからインポート]** を選択します。 ファイルをボックスにドラッグするか、 **[ファイルの参照]** を選択します。 必要であれば、アップロードした IP アドレスを確認して、編集することができます。

   IP アドレスを入力すると、ポータルによって検証が行われ、オーバーラップ、重複、書式の問題が確認されます。

5. 終わったら、 **[確認と作成]** を選択します。
6. **作成** を選択します。


## <a name="next-steps"></a>次のステップ

- [IP グループについてさらに確認する](ip-groups.md)
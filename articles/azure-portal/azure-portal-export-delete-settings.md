---
title: Azure portal の設定のエクスポートまたは削除 | Microsoft Docs
description: Azure portal のユーザー設定、プライベート ダッシュボード、カスタム設定を、エクスポートまたは削除する方法について説明します。
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: f033af37985077f4d8df9d541b55764df0c75eda
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640178"
---
# <a name="export-or-delete-user-settings"></a>ユーザー設定のエクスポートまたは削除

Azure portal の設定と機能を使って、カスタム エクスペリエンスを作成できます。 カスタム設定に関する情報は、Azure に格納されます。 次のユーザー データを削除またはエクスポートできます。

* Azure portal のプライベート ダッシュボード
* お気に入りのサブスクリプションまたはディレクトリなどのユーザーの設定、およびディレクトリの前回のログイン
* テーマとその他のカスタム ポータル設定

設定を削除する前に、エクスポートして確認するのはよいことです。 ダッシュボードの再構築やカスタム設定のやり直しには、時間がかかる場合があります。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>ポータル設定をエクスポートまたは削除する

1. [Azure portal](https://portal.azure.com) にサインインする
2. ポータルのヘッダーで、 **[設定]** を選択します。

    ![ポータル設定 (歯車) を示しているスクリーンショット](media/azure-portal-export-delete-settings/azure-portal-settings-icon.png)

3. **すべての設定をエクスポート** または **すべての設定とプライベート ダッシュ ボードを削除** を選択します。

    ![ポータルのエクスポートおよび削除の設定が示されているスクリーンショット](media/azure-portal-export-delete-settings/azure-portal-export-delete-settings.png)

      次の表ではこれらのアクションについて説明します。

      | アクション | [説明] |
      | --- | --- |
      | **すべての設定をエクスポート** | 配色テーマ、お気に入り、プライベート ダッシュボードなどのユーザー設定が含まれる .json ファイルを作成します。|
      | **すべての設定とプライベート ダッシュボードを削除** | プライベート ダッシュボードと、ポータルに対して行った他のカスタム設定へのすべてのリンクを削除します。 |

> [!NOTE]
> ユーザー設定の動的な性質とデータが破損するリスクのため、.json ファイルから設定をインポートすることはできません。
>
>


## <a name="next-steps"></a>次のステップ

* [Azure ダッシュボードの作成と共有](azure-portal-dashboard-share-access.md)
* [お気に入りの追加、削除、並べ替え](azure-portal-add-remove-sort-favorites.md)

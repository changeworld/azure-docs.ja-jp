---
title: Azure portal 設定のエクスポートまたは削除
description: Azure portal のユーザー設定、プライベート ダッシュボード、カスタム設定を、エクスポートまたは削除する方法について説明します。
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 01/29/2020
ms.topic: how-to
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: 248c643c5f1865451abbe201e7033efedb8a7e39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763943"
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

1. ポータルのヘッダーで、![設定アイコン](media/azure-portal-export-delete-settings/settings-icon.png) **設定**を選択します。

1. **すべての設定をエクスポート** または **すべての設定とプライベート ダッシュ ボードを削除** を選択します。

    ![Azure portal 設定と設定オプション](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      次の表ではこれらのアクションについて説明します。

      | アクション | 説明 |
      | --- | --- |
      | **すべての設定をエクスポート** | 配色テーマ、お気に入り、プライベート ダッシュボードなどのユーザー設定が含まれる *.json* ファイルを作成します。|
      | **すべての設定とプライベート ダッシュボードを削除** | プライベート ダッシュボードと、ポータルに対して行った他のカスタム設定へのすべてのリンクを削除します。 |

> [!NOTE]
> ユーザー設定の動的な性質とデータが破損するリスクのため、 *.json* ファイルから設定をインポートすることはできません。
>
>

## <a name="next-steps"></a>次のステップ

* [ロールベースのアクセス制御を使用して Azure ダッシュボードを共有する](azure-portal-dashboard-share-access.md)
* [お気に入りの追加、削除、並べ替え](azure-portal-add-remove-sort-favorites.md)

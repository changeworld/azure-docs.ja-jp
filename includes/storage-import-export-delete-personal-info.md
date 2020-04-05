---
title: インクルード ファイル
description: インクルード ファイル
services: azure-policy
author: craigshoemaker
ms.service: azure-policy
ms.topic: include
ms.date: 05/18/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: e6a0ded137162328fd446b65ddb4a15fa6f1db88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "67181388"
---
## <a name="deleting-personal-information"></a>個人情報の削除

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

個人情報は、インポート/エクスポート操作中の (ポータルと API を介した) インポート/エクスポート サービスに関連しています。 これらのプロセスでは、以下のようなデータが使用されます。

- 連絡先の名前
- 電話番号
- Email
- 番地
- City
- 郵便番号
- State
- 国/地方/地域
- ドライブ ID
- 運送業者のアカウント番号
- 配送追跡番号

インポート/エクスポート ジョブの作成時に、ユーザーは連絡先情報と配送先住所を入力します。 個人情報は、ジョブと、場合によってはポータル設定という最大 2 か所に保存されます。 エクスポート プロセスの **[差出人住所の詳細]** セクションで *[Save carrier and return address as default]\(既定では、運送業者と差出人住所が保存されます\)* というラベルのチェックボックスをオンにすると、個人情報はポータル設定にのみ保存されます。

個人の連絡先情報は、以下の方法で削除することができます。

- ジョブと共に保存されているデータは、ジョブが削除されると削除されます。 ユーザーは手動でジョブを削除できます。また、完了したジョブは 90 日後に自動的に削除されます。 REST API または Azure Portal を使用して手動でジョブを削除できます。 Azure Portal でジョブを削除するには、インポート/エクスポート ジョブに移動し、コマンド バーから *[削除]* をクリックします。 REST API を介してインポート/エクスポート ジョブを削除する方法については、[インポート/エクスポート ジョブの削除](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md)に関する記事を参照してください。

- ポータル設定に保存されている連絡先情報は、ポータル設定を削除して削除することができます。 ポータル設定を削除するには、次の手順を実行します。
  - [Azure portal](https://portal.azure.com) にサインインする
  - *[設定]* アイコン ![Azure の [設定] アイコン](media/storage-import-export-delete-personal-info/azure-settings-icon.png) をクリックします
  - *[すべての設定をエクスポートする]* をクリックします (現在の設定が `.json` ファイルに保存されます)。
  - *[すべての設定とプライベート ダッシュボードを削除]* をクリックして、保存した連絡先情報を含むすべての設定を削除します。

詳細については、[セキュリティ センター](https://www.microsoft.com/trustcenter)に掲載されている Microsoft のプライバシー ポリシーを確認してください。
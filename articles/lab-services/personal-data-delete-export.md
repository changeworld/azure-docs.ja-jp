---
title: Azure DevTest Labs から個人データを削除およびエクスポートする方法 | Microsoft Docs
description: Azure DevLast Labs サービスから個人データを削除およびエクスポートして、一般データ保護規則 (GDPR) での義務を果たす方法について説明します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: spelluru
ms.openlocfilehash: e681652c13e521bd33524e247db65088f47a794c
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51637148"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Azure DevTest Labs から個人データをエクスポートまたは削除する
この記事では、Azure DevTest Labs サービスから個人データを削除およびエクスポートする手順を説明します。 

## <a name="what-personal-data-does-devtest-labs-collect"></a>DevTest Labs によって収集される個人データ
DevTest Labs では、ユーザーから 2 つの主な個人データが収集されます。 これらは、ユーザーの電子メール アドレスとユーザー オブジェクト ID です。 この情報は、サービスがラボ管理者とラボ ユーザーに現在の機能を提供するために不可欠です。

### <a name="user-email-address"></a>ユーザーの電子メール アドレス
DevTest Labs では、ユーザーの電子メール アドレスを使用して、ラボ ユーザーに自動シャットダウンの電子メール通知を送信します。 電子メールでは、ユーザーのマシンがシャットダウンされることをそのユーザーに通知します。 ユーザーは、必要に応じてシャットダウンを延期またはスキップできます。 電子メール アドレスは、ラボ レベルまたは VM レベルで構成します。

**ラボでの電子メールの設定:**

![ラボ レベルでの電子メールの設定](./media/personal-data-delete-export/lab-user-email.png)

**VM での電子メールの設定:**

![VM レベルでの電子メールの設定](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>ユーザー オブジェクト ID
DevTest Labs では、ユーザー オブジェクト ID を使用して、ラボ管理者に前月比のコスト傾向とリソース別のコストを示します。 これにより、ラボのコストを追跡し、しきい値を管理することができます。 

**現在のカレンダー月の推定コスト傾向:**
![現在のカレンダー月の推定コスト傾向](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**リソース別の推定前月比コスト:**
![リソース別の推定前月比コスト](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>この個人データが必要な理由
DevTest Labs サービスでは、運用の目的で個人データを使用します。 このデータは、サービスが主要機能を提供するために不可欠です。 ユーザーの電子メール アドレスに対してアイテム保持ポリシーを設定した場合、ラボ ユーザーは、電子メール アドレスがシステムから削除された後、自動シャットダウンの電子メール通知をタイムリーに受信しません。 同様に、ラボ管理者は、アイテム保持ポリシーに基づいてユーザー オブジェクト ID が削除された場合に、ラボ内のマシンの前月比コスト傾向とリソース別コストを表示できません。 そのため、このデータは、ラボ内でユーザーのリソースがアクティブになっている限り保持する必要があります。

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>システムに個人データを破棄させる方法
ラボ ユーザーは、この個人データを削除したい場合に、ラボ内の対応するリソースを削除することによって削除できます。 DevTest Labs サービスは、ユーザーによって削除された個人データを 30 日後に匿名化します。

たとえば、VM を削除するか、電子メール アドレスを削除した場合、DevTest Labs サービスでは、リソースの削除から 30 日後にこのデータが匿名化されます。 削除後 30 日間のアイテム保持ポリシーによって、ラボ管理者には正確な前月比コスト予測が確実に提供されます。

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>個人データのエクスポートを要求する方法
ラボ ユーザーは、DevTest Labs サービスによって保存される個人データのエクスポートを要求できます。 エクスポートを要求するには、ラボの **[概要]** ページで **[個人データ]** オプションに移動します。 **[エクスポートの要求]** を選択すると、ラボ管理者のストレージ アカウント内でダウンロード可能な excel ファイルの作成が開始します。 ラボ管理者に連絡して、このデータを表示できます。

1. 左側のメニューの **[個人データ]** を選択します。 

    ![[個人データ] ページ](./media/personal-data-delete-export/personal-data-page.png)
2. ラボが含まれる**リソース グループ**を選択します。

    ![リソース グループの選択](./media/personal-data-delete-export/select-resource-group.png)
3. リソース グループ内の**ストレージ アカウント**を選択します。
4. **[ストレージ アカウント]** ページで **[BLOB]** を選択します。

    ![BLOB の選択タイル](./media/personal-data-delete-export/select-blobs-tile.png)
5. コンテナーの一覧で、**labresourceusage** という名前のコンテナーを選択します。

    ![BLOB コンテナーの選択](./media/personal-data-delete-export/select-blob-container.png)
6. ラボにちなんだ名前の**フォルダー**を選択します。 このフォルダー内に、ラボ内の**ディスク**と**仮想マシン**の **csv** ファイルが見つかります。 これらの csv ファイルをダウンロードし、アクセスを要求しているラボ ユーザーのコンテンツをフィルター処理して、それらのユーザーと共有できます。

    ![CSV ファイルのダウンロード](./media/personal-data-delete-export/download-csv-file.png)

## <a name="next-steps"></a>次の手順
次の記事を参照してください。 

- [ラボのポリシーを設定する](devtest-lab-get-started-with-lab-policies.md)
- [よく寄せられる質問](devtest-lab-faq.md)

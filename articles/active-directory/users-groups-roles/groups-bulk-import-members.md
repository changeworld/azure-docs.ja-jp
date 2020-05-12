---
title: グループのメンバーを追加または作成するための一括アップロード - Azure Active Directory |Microsoft Docs
description: Azure Active Directory 管理センターでグループ メンバーを一括して追加します。
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8902c3147bbe142fc58d4e2c3fa83601c8ccbba3
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203526"
---
# <a name="bulk-import-group-members-in-azure-active-directory"></a>Azure Active Directory でのグループ メンバーの一括インポート

Azure Active Directory (Azure AD) ポータルでは、コンマ区切り値 (CSV) ファイルを使用してグループ メンバーを一括インポートすることで、多数のメンバーをグループに追加できます。

## <a name="understand-the-csv-template"></a>CSV テンプレートについて

一括アップロード CSV テンプレートをダウンロードして入力し、Azure AD グループ メンバーを正常に一括追加します。 CSV テンプレートは、次の例のようになります。

![アップロード用のスプレッドシートと、各行および列の目的と値を説明する吹き出し](./media/groups-bulk-import-members/template-with-callouts.png)

### <a name="csv-template-structure"></a>CSV テンプレートの構造

ダウンロードした CSV テンプレート内の行は次のとおりです。

- **バージョン番号**: アップロード CSV には、バージョン番号を含む最初の行を含める必要があります。
- **列見出し**:列見出しの形式は &lt;*項目名*&gt; [PropertyName] &lt;*Required または空白*&gt; です。 たとえば、「 `Member object ID or user principal name [memberObjectIdOrUpn] Required` 」のように入力します。 テンプレートの古いバージョンの中には、微妙に異なるものもあります。 グループ メンバーシップの変更では、使用する識別子として、メンバー オブジェクト ID またはユーザー プリンシパル名のいずれかを選択できます。
- **例の行**:このテンプレートには、各列に使用できる値の例の行が含まれています。 例の行を削除し、独自のエントリに置き換える必要があります。

### <a name="additional-guidance"></a>その他のガイダンス

- アップロード テンプレートの最初の 2 行を削除または変更することはできません。アップロードを処理することができなくなります。
- 必須の列は最初に表示されます。
- テンプレートに新しい列を追加することはお勧めしません。 追加した列は無視され、処理されません。
- できる限り、常に最新バージョンの CSV テンプレートをダウンロードすることをお勧めします。

## <a name="to-bulk-import-group-members"></a>グループ メンバーを一括インポートするには

1. 組織のユーザー管理者アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。 グループの所有者も、所有しているグループのメンバーを一括インポートできます。
1. Azure AD で、 **[グループ]**  >  **[すべてのグループ]** の順に選択します。
1. メンバーを追加するグループを開き、 **[メンバー]** を選択します。
1. **[メンバー]** ページで、 **[メンバーのインポート]** を選択します。
1. **[グループ メンバーの一括インポート]** ページで、 **[ダウンロード]** を選択して、必要なグループ メンバーのプロパティを含む CSV ファイル テンプレートを取得します。

    ![[メンバーのインポート] コマンドは、グループの [プロファイル] ページにあります](./media/groups-bulk-import-members/import-panel.png)

1. CSV ファイルを開き、グループにインポートする各グループ メンバーの行を追加します (必要な値は、**メンバー オブジェクト ID** または**ユーザー プリンシパル名**です)。 そのうえでファイルを保存します。

   ![CSV ファイルには、インポートするメンバーの名前と ID が含まれています](./media/groups-bulk-import-members/csv-file.png)

1. **[グループ メンバーの一括インポート]** ページの **[CSV ファイルをアップロード]** で、そのファイルを参照します。 ファイルを選択すると、CSV ファイルの検証が開始されます。
1. ファイルの内容が検証されると、一括インポート ページに "**ファイルが正常にアップロードされました**" と表示されます。 エラーが存在する場合は、ジョブを送信する前にそれらを修正する必要があります。
1. ファイルが検証に合格したら、 **[送信]** を選択して、グループ メンバーをグループにインポートする Azure の一括操作を開始します。
1. インポート操作が完了すると、一括操作が成功したという通知が表示されます。

## <a name="check-import-status"></a>インポートの状態を確認する

**[一括操作の結果]** ページでは、保留中のすべての一括要求の状態を確認できます。

[![](media/groups-bulk-import-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-import-members/bulk-center.png#lightbox)

一括操作に含まれる各行の項目の詳細については、 **[成功数]** 、 **[失敗数]** 、 **[要求数合計]** の各列の値を選択してください。 エラーが発生した場合、その理由が表示されます。

## <a name="bulk-import-service-limits"></a>一括インポート サービスの制限

グループ メンバーの一覧をインポートする各一括アクティビティは、最大 1 時間実行できます。 これにより、少なくとも 4 万人のメンバーの一覧をインポートできます。

## <a name="next-steps"></a>次のステップ

- [グループ メンバーを一括削除する](groups-bulk-remove-members.md)
- [グループ メンバーをダウンロードする](groups-bulk-download-members.md)
- [全グループの一覧をダウンロードする](groups-bulk-download.md)

---
title: 公開済みの Azure Certified Device を編集する方法
description: Azure Certified Device プログラムを通じてデバイスを認定および公開した後に、デバイス情報を編集するためのガイドです。
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 14a29d438103f07dd35b3a3380b7cc094f215824
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304445"
---
# <a name="edit-your-published-device"></a>公開済みのデバイスの編集

デバイスを認定して Azure Certified Device カタログに公開した後に、デバイスの詳細を更新することが必要になる場合があります。 これには、ディストリビューターの一覧の更新、購入ページの URL の変更、またはハードウェア仕様 (オペレーティング システムのバージョンや新しいコンポーネントの追加など) の更新などの理由が考えられます。 Azure Certified Device ポータルを使用すると、カタログから製品を削除することなくデバイス情報を簡単に更新できます。

## <a name="prerequisites"></a>前提条件

- [Azure Certified Device ポータル](https://certify.azure.com)にサインイン済みであり、デバイス用の **承認** されたプロジェクトがある必要があります。 認定デバイスがない場合は、こちらの[チュートリアル](tutorial-01-creating-your-project.md)を参照して開始できます。

## <a name="editing-your-published-project"></a>公開済みのプロジェクトの編集

プロジェクトの概要では、プロジェクトが読み取り専用モードであることがわかります。これは、既にレビューと承認が完了しているためです。 変更を加えるには、プロジェクトに対する編集を要求し、Azure 認定チームによってその更新が再承認される必要があります。

1. ページの上部にある `Request Metadata Edit` ボタンをクリックします。  

    ![メタデータの更新を要求する](./media/images/request-metadata-edit.png)

1. ページ上で、編集後のレビュー用に製品を提出する必要があることを示す通知を確認します。
    > [!NOTE]
    > この編集を確認すると、Azure Certified Device カタログからデバイスが削除されることは **ありません** (既に公開済みの場合)。 以前のバージョンの製品は、デバイスを再公開するまでカタログに残ります。

1. この警告について確認したら、デバイスの詳細を編集できます。 `Device Details` の `Comments for Reviewer` セクションに、変更した内容についてのメモを必ず残してください。

    ![メタデータの編集についてのメモ](./media/images/edit-notes.png)

1. Azure 認定チームから変更の再承認を受けるには、プロジェクトの概要ページで `Submit for review` をクリックします。
1. 変更がレビューおよび承認されたら、ポータルを使用して変更をカタログに再公開できます ([チュートリアル](./tutorial-04-publishing-your-device.md)を参照してください)。

## <a name="next-steps"></a>次のステップ

これで、Azure Certified Device カタログのデバイスが正常に編集されました。 カタログで変更を確認したり、別のデバイスを認定したりすることができます。
- [Azure Certified Device カタログ](https://devicecatalog.azure.com/)
- [デバイスの認定を開始する](./tutorial-01-creating-your-project.md)

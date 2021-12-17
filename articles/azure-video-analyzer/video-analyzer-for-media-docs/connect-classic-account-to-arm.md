---
title: クラシック Video Analyzer for Media アカウントを ARM に接続する
description: このトピックでは、既存の有料クラシック Azure Video Analyzer for Media アカウントを ARM ベースのアカウントに接続する方法について説明します。
ms.topic: how-to
ms.author: itnorman
ms.date: 10/19/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 14ab4e2b3c95823359dc1f1f88b8368ce58afef9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312597"
---
# <a name="connect-an-existing-classic-paid-video-analyzer-for-media-account-to-arm-based-account"></a>既存の有料クラシック Video Analyzer for Media アカウントを ARM ベースのアカウントに接続する  

この記事では、既存の有料クラシック Azure Video Analyzer for Media アカウントを Azure Resource Manager (ARM) ベースのアカウントに接続する方法について詳しく説明します。
現在、Azure Video Analyzer for Media (旧称 Video Indexer) は、Azure の ARM リソースではない GA (一般提供) 製品です。
この記事では、**既存の** Azure Video Analyzer for Media アカウントを [ARM][docs-arm-overview] に接続する方法の選択肢を一通り説明します。

## <a name="prerequisites"></a>前提条件

* 無制限の有料 Azure Video Analyzer for Media アカウント (クラシック アカウント)。
  * ARM への接続アクションを実行するには、Azure Video Analyzer for Media アカウントに対する所有者のアクセス許可が必要です。
* Azure サブスクリプション。
* ユーザー割り当て済みマネージド ID (フローに沿って作成できます)。

#### <a name="transition-state-and-recommended-steps-before-connecting-a-classic-account-to-be-arm-based"></a>クラシック アカウントを ARM ベースに接続する前の移行状態と推奨される手順

接続プロセスで、アカウント管理は ARM に接続されます。これにより、30 日間のアカウントの切り替え状態が発生します。 この状態では、接続されたアカウントには、[API Management (クラシック) を使用して生成された](https://aka.ms/avam-dev-portal)アクセス トークンと ARM によって生成されたアクセス トークンの両方で API を介してアクセスできます。 切り替え状態は、すべてのアカウント管理を ARM による管理に移動するために、Azure Video Analyzer for Media ポータルからの招待ユーザー機能を無効にします。これは、ファクト アカウント管理が [Azure RBAC][docs-rbac-overview] によって処理されるためです。 これにより、このアカウントで招待されているすべてのユーザーが、Azure Video Analyzer for Media アカウント メディア ポータルへのアクセス権を失います。 もちろん、Azure RBAC の [RBAC の割り当て方法][docs-rbac-assignment]を通じてこれらすべてのユーザーに適切なロール割り当てを割り当てることによって、これを簡単に解決できます。 接続アクションを実行したアカウント オーナーのみが、接続されたアカウントの所有者として自動的に割り当てられます。 ユーザーが Azure RBAC を介してアカウントに追加されない場合、30 日後に API からもアクセスできなくなります。これは、切り替え状態の終了後は、すべてのユーザーは APIM (クラシック) を使用して有効なアクセス トークンを生成できず、ARM を介してのみになるためです。 Azure RBAC を、アカウントに対するロールベースのアクセス制御を管理するための排他的な方法として使用します。

> [!NOTE]
> 招待されたユーザーが 30 日間の切り替え状態の終了前にアクセスを削除する場合は、アカウントを ARM に接続する **前に**、Azure Video Analyzer for Media アカウント設定でのアカウント設定を使用して実行する必要があります。 

## <a name="get-started"></a>はじめに

### <a name="browse-to-video-analyzer-for-media-portal"></a>[Azure Video Analyzer for Media ポータル](https://aka.ms/vi-portal-link)に移動します。

1. AAD アカウントを使用してサイン インします。
1. 右上のバーの *[ユーザー アカウント]* をクリックして、作業ウィンドウのアカウントの一覧を開きます。
3. ARM に接続する Azure Video Analyzer for Media クラシック アカウントを選択します (クラシック アカウントはクラシック タグでタグ付けされます)。
4. [**Settings**] をクリックします。

  ![account-settings](media/connect-classic-account-to-arm/user-account-settings.png)
   
5. **[ARM ベースのアカウントに接続する]** をクリックします。

  ![connect-button-portal](media/connect-classic-account-to-arm/connect-button.png)

7. Azure portal に署名します
8. Azure Video Analyzer for Media 作成ブレードが開きます。
10. **[メディア アカウント用の Video Analyzer を作成する]** セクションに必要な値を入力します。
    * この手順に従ってフィールドを自動設定する場合は、資格のある値を必ず検証してください。

 ![connect-to-arm](media/connect-classic-account-to-arm/connect-blade-new.png)

 | 名前 | 説明 |
 | ---|---|
 |**サブスクリプション**| 現在、サブスクリプションには、クラシック アカウントおよびその他の関連リソース (Azure Media Services など) が含まれています。|
 |**リソース グループ**|既存のリソース を選択するか、新しいリソースを作成します。 リソース グループは、接続されているクラシック アカウントと同じ場所にある必要があります。|
 |**Azure Video Analyzer for Media アカウント** (ラジオ ボタン)| [既存のクラシック アカウントを接続する] を選択します。|
 |**既存のアカウント ID**| 既存の Azure Video Analyzer for Media クラシック アカウントの ID を入力します。|
 |**リソース名**|新しい Azure Video Analyzer for Media アカウントの名前を入力します。 既定値では、アカウントにクラシックと同じ名前が付けられます。|
 |**場所**|接続プロセスで地理的リージョンを変更することができます。接続されたアカウントは同じリージョンに存在する必要があります。 |
 |**Media Services アカウント名**|クラシック アカウントに関連付けられていた、元の Azure Media Services アカウント名。|
 |**ユーザー割り当てマネージド ID**|ユーザー割り当てマネージド ID を選択するか、新しい ID を作成します。 Azure Video Analyzer for Media アカウントは、メディア サービスにアクセスするためにそれを使用します。 ユーザー割り当てマネージド ID には、Azure Media Services アカウントに対して共同作成者のロールが割り当てられます。|

1. フォームの下部にある **[確認と作成]** をクリックします。

### <a name="next-steps"></a>次のステップ

[C# を使用してビデオをアップロード](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/ApiUsage/ArmBased)する方法について確認する。
  
<!-- links -->
[docs-arm-overview]: ../../azure-resource-manager/management/overview.md
[docs-rbac-overview]: ../../role-based-access-control/overview.md
[docs-rbac-assignment]: ../../role-based-access-control/role-assignments-portal.md

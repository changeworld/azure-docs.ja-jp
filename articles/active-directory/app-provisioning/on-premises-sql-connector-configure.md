---
title: Azure AD ECMA Connector Host の汎用 SQL コネクタの構成
description: このドキュメントでは、Azure AD ECMA Connector Host の汎用 SQL コネクタを構成する方法について説明します。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 06/06/2021
ms.author: billmath
ms.reviewer: arvinh
ms.openlocfilehash: a78ae13af84f3453e3a6119f163d90cd37be16bc
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437368"
---
# <a name="azure-ad-ecma-connector-host-generic-sql-connector-configuration"></a>Azure AD ECMA Connector Host の汎用 SQL コネクタの構成

>[!IMPORTANT]
> オンプレミス プロビジョニング プレビューは現在、招待のみのプレビューです。 この機能へのアクセスを要求するには、[アクセス要求フォーム](https://aka.ms/onpremprovisioningpublicpreviewaccess)を使用します。 今後数か月の間に、より多くのお客様および接続ユーザーにプレビューを公開し、一般提供に向けて準備を進めていく予定です。

この記事では、Azure Active Directory (Azure AD) ECMA Connector Host を使用して新しい SQL コネクタを作成する方法とそれを構成する方法について説明します。 Azure AD ECMA Connector Host を正常にインストールしたら、このタスクを実行する必要があります。

>[!NOTE] 
> この記事では、汎用 SQL コネクタの構成についてのみ説明します。 汎用 SQL コネクタの設定方法の手順の例については、「[ECMA Connector Host 汎用 SQL コネクタのチュートリアル](tutorial-ecma-sql-connector.md)」を参照してください。

 このフローでは、Azure AD ECMA Connector Host をインストールして構成するプロセスについて説明します。

 ![インストールのフローを示す図。](./media/on-premises-sql-connector-configure/flow-1.png)

インストールと構成の詳細については、以下を参照してください。
   - [Azure AD ECMA Connector Host の前提条件](on-premises-ecma-prerequisites.md)
   - [Azure AD ECMA Connector Host のインストール](on-premises-ecma-install.md)
   - [Azure AD ECMA Connector Host とプロビジョニング エージェントを構成する](on-premises-ecma-configure.md)

選択するオプションによっては、ウィザードの一部の画面を使用できない場合があり、情報も多少異なる可能性があります。 この構成では、ユーザー オブジェクトの種類を使用します。 次の情報を使用して構成を進めます。 

#### <a name="supported-systems"></a>サポートされるシステム
* Microsoft SQL Server と Azure SQL
* IBM DB2 10.x
* IBM DB2 9.x
* Oracle 10 および 11g
* Oracle 12c および 18c
* MySQL 5.x

## <a name="create-a-generic-sql-connector"></a>汎用 SQL コネクタを作成する

汎用 SQL コネクタを作成するには、以下のようにします。

 1. デスクトップで、ECMA Connector Host のショートカットを選択します。
 1. **[新しいコネクタ]** を選択します。
 
     ![新しいコネクタの選択を示すスクリーンショット。](.\media\on-premises-sql-connector-configure\sql-1.png)

 1. **[プロパティ]** ページで、ボックスに入力し、 **[次へ]**  を選択します。 個々のボックスのガイダンスについては、画像の下にある表を参照してください。
 
     ![プロパティの入力を示すスクリーンショット。](.\media\on-premises-sql-connector-configure\sql-2.png)

     |プロパティ|説明|
     |-----|-----|
     |名前|このコネクタの名前。|
     |AutoSync タイマー (分)|許容される最小値は 120 分です。|
     |シークレット トークン|123456 (このトークンは、10 から 20 桁の ASCII 文字と数字で構成される文字列である必要があります。)|
     |説明|コネクタの説明。|
     |拡張 DLL|汎用 SQL コネクタの場合、**Microsoft.IAM.Connector.GenericSql.dll** を選択します。|
 1. **[接続]** ページで、ボックスに入力し、 **[次へ]** を選択します。 個々のボックスのガイダンスについては、画像の下にある表を参照してください。
 
     ![接続の入力を示すスクリーンショット。](.\media\on-premises-sql-connector-configure\sql-3.png)

     |プロパティ|説明|
     |-----|-----|
     |DSN ファイル|SQL Server インスタンスへの接続に使用するデータ ソース名ファイル|
     |[ユーザー名]|SQL Server インスタンスに対する権限を持つ個人のユーザー名。 スタンドアロン サーバーの場合は hostname\sqladminaccount で、ドメイン メンバー サーバーの場合は domain\sqladminaccount の形式を取る必要があります。|
     |パスワード|上記で指定したユーザー名のパスワード。|
     |DN is Anchor \(DN はアンカー\)|お使いの環境でこれらの設定が必要であるとわかっている場合を除いて、 **[DN is Anchor]\(DN はアンカー\)** および **[エクスポートの種類: オブジェクトの置換]** チェックボックスを選択しないでください。|
     |エクスポートの種類: オブジェクトの置換||
 1. **[スキーマ 1]** ページで、ボックスに入力し、 **[次へ]** を選択します。 個々のボックスのガイダンスについては、画像の下にある表を参照してください。
 
     ![[スキーマ 1] ページを示すスクリーンショット。](.\media\on-premises-sql-connector-configure\sql-4.png)

     |プロパティ|説明|
     |-----|-----|
     |オブジェクトの種類の検出方法|コネクタをプロビジョニングするオブジェクトの種類の検出に使用する方法。|
     |固定値のリスト/テーブル/ビュー/SP|このボックスには **ユーザー** が含まれている必要があります。|
     |テーブル/ビュー/SP の列名||
     |ストアド プロシージャのパラメーター||
     |オブジェクトの種類を検出するための SQL クエリを指定します||
 1. **[スキーマ 2]** ページで、ボックスに入力し、 **[次へ]** を選択します。 個々のボックスのガイダンスについては、画像の下にある表を参照してください。 このスキーマ画面は、前の手順で選択したオブジェクトの種類に応じて、多少異なる場合や追加情報が表示される場合があります。
 
     ![[スキーマ 2] ページを示すスクリーンショット。](.\media\on-premises-sql-connector-configure\sql-5.png)

     |プロパティ|説明|
     |-----|-----|
     |ユーザー: 属性の検出|このプロパティは、 **[テーブル]** に設定する必要があります。|
     |ユーザー: テーブル/ビュー/SP|このボックスには **従業員** を含める必要があります。|
     |ユーザー: 複数値のテーブル/ビューの名前||
     |ユーザー: ストアド プロシージャのパラメーター||
     |ユーザー: 属性を検出するための SQL クエリを指定||
 1. **[スキーマ 3]** ページで、ボックスに入力し、 **[次へ]** を選択します。 個々のボックスのガイダンスについては、画像の下にある表を参照してください。 表示される属性は、前の手順で指定した情報によって異なります。
 
     ![[スキーマ 3] ページを示すスクリーンショット。](.\media\on-premises-sql-connector-configure\sql-6.png)

     |プロパティ|説明|
     |-----|-----|
     |ユーザーの DN 属性を選択してください||
 1. **[スキーマ 4]** ページで、コネクタの **DataType** 属性とフローの方向を確認します。 必要に応じてこれらを調整し、 **[次へ]** を選択します。
 
     ![[スキーマ 4] ページを示すスクリーンショット。](.\media\on-premises-sql-connector-configure\sql-7.png)  
 1. **[グローバル]** ページで、ボックスに入力し、 **[次へ]** を選択します。 個々のボックスのガイダンスについては、画像の下にある表を参照してください。
 
     ![[グローバル] ページを示すスクリーンショット。](.\media\on-premises-sql-connector-configure\sql-8.png)

     |プロパティ|説明|
     |-----|-----|
     |ウォーター マーク クエリ||
     |データ ソースのタイム ゾーン|データ ソースの所在地のタイム ゾーンを選択します。|
     |データ ソースの日付と時刻の形式|データ ソースの形式を指定します。|
     |名前付きパラメーターを使用してストアド プロシージャを実行する||
     |操作方法||
     |拡張機能の名前||
     |パスワード SP 名の設定||
     |パスワード SP パラメーターの設定||
 1. **[パーティションの選択]** ページで、正しいパーティションを確実に選択し、 **[次へ]** を選択します。
 
     ![[パーティションの選択] ページを示すスクリーンショット。](.\media\on-premises-sql-connector-configure\sql-9.png)

 1. **[実行プロファイル]** ページで、使用する実行プロファイルを選択し、 **[次へ]** を選択します。
 
     ![[実行プロファイル] ページを示すスクリーンショット。](.\media\on-premises-sql-connector-configure\sql-10.png)

     |プロパティ|説明|
     |-----|-----|
     |エクスポート|データを SQL にエクスポートする実行プロファイル。 この実行プロファイルは必須です。|
     |フル インポート|前に指定した SQL ソースからすべてのデータをインポートする実行プロファイル。|
     |差分インポート|最後のフルまたは差分インポート以降の SQL からの変更のみをインポートする実行プロファイル。|
 
 1. **[実行プロファイル]** ページで、ボックスに入力し、 **[次へ]** を選択します。 個々のボックスのガイダンスについては、画像の下にある表を参照してください。
  
     ![「エクスポート情報を入力する」を示すスクリーンショット。](.\media\on-premises-sql-connector-configure\sql-11.png)

     |プロパティ|説明|
     |-----|-----|
     |操作方法||
     |テーブル/ビュー/SP||
     |開始インデックス パラメーター名||
     |終了インデックス パラメーター名||
     |ストアド プロシージャのパラメーター||
 
 1. **[オブジェクトの種類]** ページで、ボックスに入力し、 **[次へ]** を選択します。 個々のボックスのガイダンスについては、画像の下にある表を参照してください。 
 
     ![[オブジェクトの種類] ページを示すスクリーンショット。](.\media\on-premises-sql-connector-configure\sql-12.png)

     |プロパティ|説明|
     |-----|-----|
     |ターゲット オブジェクト|構成しているオブジェクト。|
     |アンカー|オブジェクトのアンカーとして使用する属性。 この属性は、ターゲット システム内で一意である必要があります。 Azure AD プロビジョニング サービスでは、初期サイクル後、この属性を使用して ECMA ホストに対してクエリを実行します。 このアンカー値は、スキーマ 3 のアンカー値と同じである必要があります。|
     |クエリ属性|ECMA ホストでメモリ内キャッシュに対してクエリを実行するために使用されます。 この属性は一意である必要があります。|
     |DN|ターゲット オブジェクトの識別名に使用される属性。 ほとんどの場合、 **[Autogenerated]\(自動生成\)** チェックボックスをオンにする必要があります。 オフになっている場合、DN 属性が、CN = anchorValue, Object = objectType の形式で、DN を格納している Azure AD 内の属性に確実にマップされるようにします。|
 
 1. ECMA ホストでは、ターゲット システムでサポートされる属性が検出されます。 Azure AD に公開する属性を選択できます。 プロビジョニングのために、これらの属性を Azure portal で構成できます。 **[属性の選択]** ページで、ドロップダウン リストから追加する属性を選択します。
 
     ![[属性の選択] ページを示すスクリーンショット。](.\media\on-premises-sql-connector-configure\sql-13.png)

1. **[プロビジョニング解除]** ページで、プロビジョニング解除情報を確認し、必要に応じて調整を行います。 前のページで選択した属性を **[プロビジョニング解除]** ページで選択することはできません。 **[完了]** を選択します。

     ![プロビジョニング解除のページを示すスクリーンショット。](.\media\on-premises-sql-connector-configure\sql-14.png)

## <a name="next-steps"></a>次のステップ

- [アプリ プロビジョニング](user-provisioning.md)
- [Azure AD ECMA Connector Host のインストール](on-premises-ecma-install.md)
- [Azure AD ECMA Connector Host の構成](on-premises-ecma-configure.md)
- [チュートリアル: ECMA Connector Host 汎用 SQL コネクタ](tutorial-ecma-sql-connector.md)

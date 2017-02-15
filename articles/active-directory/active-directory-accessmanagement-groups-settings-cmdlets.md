---
title: "グループの設定を構成するための Azure Active Directory コマンドレット | Microsoft Docs"
description: "Azure Active Directory コマンドレットを使用して、グループの設定を管理する方法です。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 9f2090e6-3af4-4f07-bbb2-1d18dae89b73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: c18ef38661e31e16114b88bdfad36320776ef12b


---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>グループの設定を構成するための Azure Active Directory コマンドレット
ディレクトリでは、統合グループに対して次の設定を構成できます。

1. 分類: ユーザーがグループに設定できる分類のコンマ区切りの一覧です。 例としては、「分類」、「シークレット」および「最上位のシークレット」があります。
2. 使用ガイドラインの URL: 組織で定義されている統合グループを使用するための使用条件を表示する URL です。 この URL は、ユーザーがグループを使用するときのユーザー インターフェイスに表示されます。
3. グループ作成の有効化: 一部またはすべてのユーザーが統合グループを作成できるか、またはユーザーが統合グループを作成できないかを指定します。 オンに設定すると、すべてのユーザーがグループを作成できます。 オフに設定すると、ユーザーはグループを作成できません。 オフにする場合は、セキュリティ グループを指定することもできます。このグループのユーザーは、グループの作成が許可されます。

これらの設定は、Settings および SettingsTemplate オブジェクトを使用して構成します。 最初は、ディレクトリには Settings オブジェクトが表示されません。 つまり、ディレクトリは既定の設定で構成されています。 既定の設定を変更するには、Settings テンプレートを使用して新しい Settings オブジェクトを作成します。 Settings テンプレートは、Microsoft によって定義されます。

これらの操作で使用するコマンドレットを含むモジュールは、 [Microsoft Connect サイト](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)からダウンロードできます。

## <a name="create-settings-at-the-directory-level"></a>ディレクトリ レベルでの設定の作成
次の手順では、ディレクトリ内のすべての Office グループに適用される設定をディレクトリ レベルで作成します。

1. 使用する SettingTemplate を把握していない場合、このコマンドレットでは、Settings テンプレートの一覧が返されます。

    `Get-MsolAllSettingTemplate`

    ![設定テンプレートの一覧](./media/active-directory-accessmanagement-groups-settings-cmdlets/list-of-templates.png)
2. 使用ガイドラインの URL を追加するには、まず使用ガイドラインの URL 値を定義する SettingsTemplate オブジェクト、つまり、Group.Unified テンプレートを取得する必要があります。

    `$template = Get-MsolSettingTemplate –TemplateId 62375ab9-6b52-47ed-826b-58e47e0e304b`
3. 次に、そのテンプレートに基づいて新しい Settings オブジェクトを作成します。

    `$setting = $template.CreateSettingsObject()`
4. その後、使用ガイドラインの値を更新します。

    `$setting["UsageGuidelinesUrl"] = "<https://guideline.com>"`
5. 最後に、設定を適用します。

    `New-MsolSettings –SettingsObject $setting`

    ![使用ガイドラインの URL の追加](./media/active-directory-accessmanagement-groups-settings-cmdlets/add-usage-guideline-url.png)

Group.Unified SettingsTemplate で定義される設定は次のとおりです。

| **設定** | **説明** |
| --- | --- |
|  <ul><li>ClassificationList<li>型: 文字列<li>既定値: “” |統合グループに適用できる有効な分類の値のコンマ区切りの一覧。 |
|  <ul><li>EnableGroupCreation<li>型: ブール<li> 既定: True |ディレクトリで統合グループの作成を許可するかどうかを示すフラグ。 |
|  <ul><li>GroupCreationAllowedGroupId<li>型: 文字列<li>既定値: “” |EnableGroupCreation == false の場合でも、セキュリティ グループの GUID は統合グループの作成が許可されます。 |
|  <ul><li>UsageGuidelinesUrl<li>型: 文字列<li>既定値: “” |グループ使用ガイドラインへのリンク。 |

## <a name="read-settings-at-the-directory-level"></a>ディレクトリ レベルでの設定の読み取り
次の手順では、ディレクトリ内のすべての Office グループに適用される設定をディレクトリ レベルで読み取ります。

1. 既存のディレクトリ設定をすべて読み取ります。

    `Get-MsolAllSettings`
2. 特定のグループの設定をすべて読み取ります。

    `Get-MsolAllSettings -TargetType Groups -TargetObjectId <groupObjectId>`
3. SettingId GUID を使用して、特定のディレクトリ設定を読み取ります。

    `Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

    ![設定 ID の GUID](./media/active-directory-accessmanagement-groups-settings-cmdlets/settings-id-guid.png)

## <a name="update-settings-at-the-directory-level"></a>ディレクトリ レベルでの設定の更新
次の手順では、ディレクトリ内のすべての Office グループに適用される設定をディレクトリ レベルで更新します。

1. 既存の Settings オブジェクトを取得します。

    `$setting = Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`
2. 更新する値を取得します。

    `$value = $Setting.GetSettingsValue()`
3. 値を更新します。

    `$value["AllowToAddGuests"] = "false"`
4. 設定を更新します。

    `Set-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c –SettingsValue $value`

## <a name="remove-settings-at-the-directory-level"></a>ディレクトリ レベルでの設定の削除
次の手順では、ディレクトリ内のすべての Office グループに適用される設定をディレクトリ レベルで削除します。

    `Remove-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

## <a name="cmdlet-syntax-reference"></a>コマンドレット構文リファレンス
Azure Active Directory PowerShell のその他のドキュメントについては、 [Azure Active Directory コマンドレット](http://go.microsoft.com/fwlink/p/?LinkId=808260)を参照してください。

## <a name="settingstemplate-object-reference-groupunified-settingstemplate-object"></a>SettingsTemplate オブジェクトリファレンス (Group.Unified SettingsTemplate オブジェクト)
* "name": "EnableGroupCreation", "type": "System.Boolean", "defaultValue": "true", "description": "統合グループの作成機能がオンになっているかどうかを示すブール型のフラグ。"
* "name": "GroupCreationAllowedGroupId", "type": "System.Guid", "defaultValue": "", "description": "統合グループを作成するためのホワイトリストに登録されているセキュリティ グループの GUID。"
* "name": "ClassificationList", "type": "System.String", "defaultValue": "", "description": "統合グループに適用できる有効な分類の値のコンマ区切りの一覧。"
* "name": "UsageGuidelinesUrl", "type": "System.String", "defaultValue": "", "description": "グループ使用ガイドラインへのリンク。"

| name | type | defaultValue | 説明 |
| --- | --- | --- | --- |
| "EnableGroupCreation" |"System.Boolean" |"true" |"統合グループの作成機能がオンになっているかどうかを示すブール型のフラグ。" |
| "GroupCreationAllowedGroupId" |"System.Guid" |"" |"統合グループを作成するためのホワイトリストに登録されているセキュリティ グループの GUID。" |
| "ClassificationList" |"System.String" |"" |"統合グループに適用できる有効な分類の値のコンマ区切りの一覧。" |
| "UsageGuidelinesUrl" |"System.String" |"" |"グループ使用ガイドラインへのリンク。" |

## <a name="next-steps"></a>次のステップ
Azure Active Directory PowerShell のその他のドキュメントについては、 [Azure Active Directory コマンドレット](http://go.microsoft.com/fwlink/p/?LinkId=808260)を参照してください。

「 [Rob's Groups Blog (Rob のグループ ブログ)](http://robsgroupsblog.com/blog/configuring-settings-for-office-365-groups-in-azure-ad)」では、マイクロソフトのプログラム マネージャーである Rob de Jong がさらに詳しく説明しています。

* [Azure Active Directory グループによるリソースのアクセス管理](active-directory-manage-groups.md)
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)



<!--HONumber=Dec16_HO5-->



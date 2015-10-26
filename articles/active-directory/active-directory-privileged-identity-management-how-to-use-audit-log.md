<properties
   pageTitle="Azure Privileged Identity Management: 監査ログを使用する方法"
   description="Azure Privileged Identity Management 拡張機能で監査ログを使用する方法について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="IHenkel"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/21/2015"
   ms.author="inhenk"/>

# Azure Privileged Identity Management: 監査ログを使用する方法

## 監査ログの使用
Privileged Identity Management の監査ログを使用すると、特定の期間におけるすべてのユーザー割り当てとアクティブ化を確認できます。

## 監査ログへの移動
PIM ダッシュボードで監査履歴をクリックして、監査ログにアクセスできます。

## 監査ログのグラフ
監査ログを使用すると、合計アクティブ化数、1 日あたりの最大アクティブ化数、1 日あたりの平均アクティブ化数を折れ線グラフで表示できます。監査履歴に複数のロールがある場合は、ロールによってデータをフィルターすることもできます。

**[時間]**、**[アクション]**、**[ロール]** ボタンを使用してそれぞれのデータで並べ替えることができます。

## 監査ログの一覧
監査ログの一覧の列は次のとおりです。

- **要求元** - ロールのアクティブ化を要求したユーザー。
- **ユーザー** - ロールのアクティブ化のユーザー。
- **ロール** - ユーザーに割り当てられたロール。
- **アクション** - ロール/ユーザーで実行されるアクション。
- **時間** - アクションが発生した時刻。
- **理由** - アクティブ化の間に理由フィールドに入力されたテキスト。
- **有効期限** - 有効期限の年が 9999 の場合、ユーザーのロールは無期限。

## 監査ログのフィルター

**[フィルター]** ボタンをクリックすることにより、監査ログに表示される情報をフィルターすることもできます。**[グラフ パラメーターの更新]** ブレードが表示されます。

### 日付範囲を変更する
監査ログの時間の範囲を変更するには、**[今日]**、**[過去 1 週間]**、**[過去 1 か月]**、**[カスタム]** のいずれかのボタンを選択します。**[カスタム]** ボタンを選択すると、**[開始]** および **[終了]** 日付フィールドが表示されて、ログの日付の範囲を指定できます。MM/DD/YYYY の形式で日付を入力するか、**カレンダー** アイコンをクリックしてカレンダーから日付を選択できます。

### ログに含まれるロールを変更する

ログにロールを含めたり除外したりするには、各ロールの横にある **[ロール]** チェック ボックスをオンまたはオフにします。

監査ログのすべてのフィルターを設定したら、[更新] をクリックしてログ内のデータをフィルターします。データがすぐに表示されない場合は、**[更新]** ボタンをクリックします。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=Oct15_HO3-->
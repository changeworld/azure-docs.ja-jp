---
title: Microsoft Sentinel エンティティ タイムラインのアクティビティをカスタマイズする | Microsoft Docs
description: Microsoft Sentinel のエンティティ ページのタイムラインで追跡および表示される内容に、カスタマイズしたアクティビティを追加します
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: c787600e29f7e813ca8e27ac827803c572986ff1
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721425"
---
# <a name="customize-activities-on-entity-page-timelines"></a>エンティティ ページのタイムラインのアクティビティをカスタマイズする

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - アクティビティのカスタマイズは、**プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="introduction"></a>はじめに

既定で用意されている Microsoft Sentinel のタイムラインで追跡および表示されるアクティビティに加えて、追跡するその他のアクティビティを作成し、それらもタイムラインに表示できます。 接続されている任意のデータ ソースのエンティティ データのクエリに基づいて、カスタマイズされたアクティビティを作成できます。 たとえば、次の方法でこの機能を使用できます。

- 既定で用意されている既存のアクティビティ テンプレートを変更して、エンティティ タイムラインに新しいアクティビティを追加する。

- カスタム ログから新しいアクティビティを追加する。たとえば、特定の建物にユーザーが出入りするアクティビティを、物理的なアクセス制御ログからそのユーザーのタイムラインに追加できます。

## <a name="getting-started"></a>作業の開始

1. Microsoft Azure Sentinel のナビゲーション メニューから、 **[Entity behavior]\(エンティティの動作\)** を選択します。

1. **[エンティティの動作]** ブレードで、画面の上部にある **[エンティティ ページのカスタマイズ]** を選択します。

    :::image type="content" source="./media/customize-entity-activities/entity-behavior-blade.png" alt-text="[エンティティの動作] ページ":::

1. 表示されるページの **[自分のアクティビティ]** タブに、自分が作成したアクティビティの一覧が表示されます。 **[アクティビティ テンプレート]** タブには、Microsoft のセキュリティ研究員によって既定で用意されているアクティビティのコレクションが表示されます。 これらは、エンティティ ページのタイムラインで既に追跡および表示されているアクティビティです。

    > [!NOTE]
    > - ユーザー定義アクティビティを作成していない限り、エンティティ ページの **[アクティビティ テンプレート]** タブの下にすべてのアクティビティが表示されます。
    >
    > - 1 つのカスタム アクティビティを定義すると、エンティティ ページの **[自分のアクティビティ]** タブにそれらのアクティビティ **のみ** が表示されます。
    >
    > - エンティティ ページで、すぐに利用できるアクティビティを引き続き表示する場合は、追跡して表示するテンプレートごとにアクティビティを作成する必要があります。 以下の「アクティビティをテンプレートから作成する」の手順に従います。

## <a name="create-an-activity-from-a-template"></a>アクティビティをテンプレートから作成する

1. **[アクティビティ テンプレート]** タブをクリックすると、既定で使用できるさまざまなアクティビティが表示されます。 エンティティの種類とデータ ソースでこの一覧をフィルター処理できます。 一覧からアクティビティを選択すると、プレビュー ペインに次の詳細が表示されます。

    -  アクティビティの説明

    - アクティビティを構成するイベントを提供するデータ ソース

    - 生データ内のエンティティを識別するために使用される識別子

    - このアクティビティの検出につながるクエリ

1. プレビュー ペインの下部にある **[アクティビティの作成]** ボタンをクリックして、アクティビティ作成ウィザードを開始します。

    :::image type="content" source="./media/customize-entity-activities/activity-details.png" alt-text="アクティビティの詳細を表示":::

1. **[アクティビティ ウィザード - テンプレートからの新しいアクティビティの作成]** が開きます。フィールドにはテンプレートのデータが既に設定されています。 **[全般]** タブと **[アクティビティの構成]** タブで自由に変更を加えるか、そのままの状態で、すぐに利用できるアクティビティを引き続き表示できます。

1. 問題がなければ、 **[確認と作成]** タブを選択します。 **[検証に成功しました]** というメッセージが表示されたら、下部にある **[作成]** ボタンをクリックします。

## <a name="create-an-activity-from-scratch"></a>アクティビティを最初から作成する

[アクティビティ] ページの上部にある **[アクティビティの追加]** をクリックして、アクティビティ作成ウィザードを開始します。

**[アクティビティ ウィザード - 新しいアクティビティの作成]** が開きます。フィールドは空白になっています。

### <a name="general-tab"></a>[全般] タブ
1. アクティビティの名前を入力します (例: "グループに追加されたユーザー")。

1. アクティビティの説明を入力します (例: "Windows イベント ID 4728 に基づくユーザー グループ メンバーシップの変更")。

1. このクエリで追跡するエンティティの種類 (ユーザーまたはホスト) を選択します。

1. 追加のパラメーターでフィルター処理すると、クエリを絞り込んだり、パフォーマンスを最適化したりするのに役立ちます。 たとえば、**IsDomainJoined** パラメーターを選択して、その値を **True** に設定すると、Active Directory ユーザーをフィルター処理できます。

1. アクティビティの初期状態を **[有効]** または **[無効]** に選択できます。

1. **[次へ: アクティビティの構成]** を選択して、次のタブに進みます。

    :::image type="content" source="./media/customize-entity-activities/create-new-activity.png" alt-text="スクリーンショット - 新しいアクティビティを作成する":::

### <a name="activity-configuration-tab"></a>[アクティビティの構成] タブ

#### <a name="writing-the-activity-query"></a>アクティビティ クエリの作成

こちらでは、選択したエンティティのアクティビティを検出するために使用する KQL クエリを作成するか貼り付けます。そして、タイムラインでそれをどのように表示するかを決定します。

イベントの関連付けやカスタム アクティビティの検出のために、KQL ではエンティティの種類に応じた複数のパラメーターの入力が必要になります。 それらのパラメーターは対象となるエンティティのさまざまな識別子です。

クエリ結果とエンティティの間に一対一のマッピングを作成するため、強い識別子を選択することをお勧めします。 弱い識別子を選択すると、不正確な結果になるおそれがあります。 [エンティティと強い識別子および弱い識別子の詳細をご確認ください](entities.md)。

次の表で、エンティティの識別子に関する情報を示します。

**アカウントとホストのエンティティの強い識別子**

クエリには少なくとも 1 つの識別子が必要です。

| Entity | 識別子 | 説明 |
| - | - | - |
| **アカウント** | Account_Sid | Active Directory 内のアカウントのオンプレミス SID |
| | Account_AadUserId | Azure Active Directory 内のユーザーの Azure AD オブジェクト ID |
| | Account_Name + Account_NTDomain | SamAccountName と同様 (例: Contoso\Joe) |
| | Account_Name + Account_UPNSuffix | UserPrincipalName と同様 (例: Joe@Contoso.com) |
| **Host** | Host_HostName + Host_NTDomain | 完全修飾ドメイン名 (FQDN) と同様 |
| | Host_HostName + Host_DnsDomain | 完全修飾ドメイン名 (FQDN) と同様 |
| | Host_NetBiosName + Host_NTDomain | 完全修飾ドメイン名 (FQDN) と同様 |
| | Host_NetBiosName + Host_DnsDomain | 完全修飾ドメイン名 (FQDN) と同様 |
| | Host_AzureID | Azure Active Directory 内のホストの Azure AD オブジェクト ID (AAD ドメイン参加済みの場合) |
| | Host_OMSAgentID | 特定のホストにインストールされているエージェントの OMS エージェント ID (ホストごとに一意) |
|

選択したエンティティに基づいて、使用可能な識別子が表示されます。 関連する識別子をクリックすると、その識別子がクエリ内のカーソルの位置に貼り付けられます。

> [!NOTE]
> - クエリには **最大 10 個のフィールド** が含まれる場合があるため、必要なフィールドを投影する必要があります。
>
> - 検出されたアクティビティをエンティティのタイムラインに配置するには、投影されたフィールドに **TimeGenerated** フィールドが含まれている必要があります。

```kusto
SecurityEvent
| where EventID == "4728"
| where (SubjectUserSid == '{{Account_Sid}}' ) or (SubjectUserName == '{{Account_Name}}' and SubjectDomainName == '{{Account_NTDomain}}' )
| project TimeGenerated, SubjectUserName, MemberName, MemberSid, GroupName=TargetUserName
```

:::image type="content" source="./media/customize-entity-activities/new-activity-query.png" alt-text="スクリーンショット - アクティビティを検出するクエリを入力する":::

#### <a name="presenting-the-activity-in-the-timeline"></a>タイムラインでのアクティビティの表示

便宜上、アクティビティの出力に動的パラメーターを追加することで、タイムラインでのアクティビティの表示方法を決定できます。

Microsoft Sentinel には、使用できる組み込みのパラメーターが用意されています。また、クエリで投影したフィールドに基づいて他のパラメーターを使用できます。

パラメーターには `{{ParameterName}}` という形式を使用します。

アクティビティ クエリが検証に合格し、クエリ ウィンドウの下に **[クエリ結果の表示]** リンクが表示された後、 **[使用可能な値]** セクションを展開して、動的アクティビティ タイトルの作成時に使用できるパラメーターを表示できます。

特定のパラメーターの横にある **[コピー]** アイコンを選択して、そのパラメーターをクリップボードにコピーし、上の **[アクティビティ タイトル]** フィールドに貼り付けることができます。

次のパラメーターをクエリに追加します。

- クエリで投影した任意のフィールド。

- クエリに挙げられているエンティティのエンティティ識別子。

- `StartTimeUTC` – アクティビティの開始時刻 (UTC 時刻) を追加します。

- `EndTimeUTC` – アクティビティの終了時刻 (UTC 時刻) を追加します。

- `Count` – 複数の KQL クエリ出力を 1 つの出力にまとめます。

    `count` パラメーターは、エディターに完全に表示されていない場合でも、次のコマンドをバックグラウンドでクエリに追加します。

    ```kql
    Summarize count() by <each parameter you’ve projected in the activity>
    ```

    次に、エンティティページで **[バケット サイズ]** フィルターを使用すると、バックグラウンドで実行されるクエリにも次のコマンドが追加されます。

    ```kql
    Summarize count() by <each parameter you’ve projected in the activity>, bin (TimeGenerated, Bucket in Hours)
    ```

次に例を示します。

:::image type="content" source="./media/customize-entity-activities/new-activity-title.png" alt-text="スクリーンショット - アクティビティのタイトルに使用できる値を確認する":::

クエリとアクティビティのタイトルに問題がなければ、 **[次へ: 確認]** を選択します。

### <a name="review-and-create-tab"></a>[確認と作成] タブ

1. カスタム アクティビティのすべての構成情報を確認します。

1. **[検証に成功しました]** というメッセージが表示されたら、 **[作成]** をクリックしてアクティビティを作成します。 **[自分のアクティビティ]** タブで後からこれを編集または変更できます。

## <a name="manage-your-activities"></a>アクティビティを管理する 

カスタム アクティビティは、 **[自分のアクティビティ]** タブから管理します。アクティビティの行の最後にある省略記号 ([...]) をクリックすると、次の操作を行えます。

- アクティビティを編集する。
- アクティビティを複製して、わずかに異なるものを新しく作成する。
- アクティビティを削除する。
- アクティビティ無効にする (削除はしない)。

## <a name="view-activities-in-an-entity-page"></a>エンティティ ページでアクティビティを表示する

エンティティ ページを表示するたびに、そのエンティティに対して有効になっているすべてのアクティビティ クエリが実行され、そのエンティティのタイムラインに最新の情報が表示されます。 タイムラインには、アクティビティの他にアラートとブックマークも表示されます。 

**[タイムライン コンテンツ]** フィルターを使用すると、アクティビティのみ (またはアクティビティ、アラート、ブックマークの任意の組み合わせ) を表示できます。  

**[アクティビティ]** フィルターを使用して、特定のアクティビティを表示または非表示にすることもできます。 

## <a name="next-steps"></a>次の手順

このドキュメントでは、エンティティ ページのタイムラインにカスタム アクティビティを作成する方法について説明しました。 Microsoft Azure Sentinel の詳細については、次の記事を参照してください。
- [エンティティ ページ](identify-threats-with-entity-behavior-analytics.md)の詳細を確認する。
- [エンティティと識別子](entities-reference.md)の完全な一覧を確認する。

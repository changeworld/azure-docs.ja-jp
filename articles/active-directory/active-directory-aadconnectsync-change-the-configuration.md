<properties
	pageTitle="Azure AD Connect Sync: 既定の構成を変更する方法 | Microsoft Azure"
	description="Azure AD Connect Sync の構成に変更する方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2016"
	ms.author="andkjell"/>


# Azure AD Connect Sync: 既定の構成を変更する方法
このトピックの目的は、Azure AD Connect Sync の既定の構成を変更する方法について説明することです。ここでは、いくつかの一般的なシナリオの手順を紹介します。この知識があれば、独自のビジネス ルールに基づき独自の構成に対して簡単な変更を加えることができます。

## 同期規則エディター
同期規則エディターは、既定の構成を表示したり変更したりする際に使用されます。これは、[スタート] メニューの **[Azure AD Connect]** グループにあります。![Start Menu with Sync Rule Editor](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

このエディターを開くと、既定の標準の規則が表示されます。

![Sync Rule Editor](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### エディターのナビゲーション
エディターの上部にあるドロップダウンを使用すると、特定の規則をすばやく見つけることができます。たとえば、proxyAddresses 属性が含まれる規則を表示する場合は、ドロップダウンを次のように変更します。![SRE filtering](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png) フィルター処理をリセットし、新しい構成を読み込むには、キーボードの **F5** キーを押します。

右上に、**[Add new rule (新しい規則の追加)]** ボタンがあります。このボタンは、独自のカスタム規則の作成に使用します。

下部には、選択した同期規則に対する操作を行うためのボタンがあります。**[編集]** と **[削除]** をクリックすると、期待どおりの操作が行われます。**[エクスポート]** をクリックすると、同期規則を再作成するための PowerShell スクリプトが生成されます。この方法を使用すると、あるサーバーから別のサーバーへ同期規則を移動できます。

## 初めてのカスタム規則の作成
最も一般的な変更は、属性フローに対する変更です。ソース ディレクトリ内のデータは、Azure AD のデータに対する方法とは異なる可能性があります。このセクションの例では、常にユーザーの名前の**大文字小文字が正しくなる**ようにします。

### スケジューラを無効にする
[スケジューラ](active-directory-aadconnectsync-feature-scheduler.md)は、既定で 30 分ごとに実行されます。変更中にスケジューラが起動されないようにし、新しい規則のトラブルシューティングを行う必要があります。スケジューラを一時的に無効にするには、PowerShell を起動し、`Set-ADSyncScheduler -SyncCycleEnabled $false` を実行します。

![Disable the scheduler](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)

### 規則を作成する

1. **Add new rule (新しい規則の追加)** をクリックします。
2. **[説明]** ページで、次のように入力します。![Inbound rule filtering](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)
	- 名前: 規則にわかりやすい名前を付けます。
	- 説明: 規則の目的が他のユーザーにもわかるように説明します。
	- Connected system (接続されたシステム): オブジェクトがあるシステムです。この場合、Active Directory コネクタを選択します。
	- Connected System/Metaverse Object Type (接続されたシステム/メタバース オブジェクトの種類): それぞれ **[ユーザー]** と **[Person (人)]** を選択します。
	- リンクの種類: この値を **[結合]** に変更します。
	- Precedence (優先順位): システム内で一意になる値を指定します。指定する数値が小さいほど、優先順位が高くなることを示します。
	- タグ: 空のままにします。Microsoft が提供する標準の規則の場合のみ、このボックスには値が設定されています。
3. **[Scoping filter (スコープ フィルター)]** ページで、「**givenName ISNOTNULL**」と入力します。![Inbound rule scoping filter](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png) このセクションを使用して、規則を適用するオブジェクトを定義します。空のままにした場合は、すべてのユーザー オブジェクトに規則が適用されます。ただし、それには、会議室、サービス アカウントなど、人以外のユーザー オブジェクトも含まれます。
4. **[Join rules (結合規則)]** では、空白のままにします。
5. **[Transformations (変換)]** ページで、[FlowType] を **[式]** に変更します。[Target Attribute (ターゲット属性)] で **[givenName]** を選択し、[ソース] に「`PCase([givenName])`」と入力します。![Inbound rule transformations](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png) 同期エンジンでは、関数名と属性名の両方で大文字小文字が区別されます。入力した内容が間違っている場合は、規則を追加するときに警告が表示されます。このエディターでは、保存して続行することが可能なため、規則を再度開き、修正する必要があります。
6. **[追加]** をクリックして規則を保存します。

新しいカスタム規則は、システム内の他の同期規則と共に表示されます。

### 変更を確認する
この新しい変更により、その変更が正常に動作していて、エラーがスローされていないことを確認します。所有するオブジェクトの数に応じて、この手順を行う方法は 2 とおりあります。

1. すべてのオブジェクトに対して完全同期を実行する
2. 1 つのオブジェクトに対してプレビューと完全同期を実行する

[スタート] メニューから **[Synchronization Service (同期サービス)]** を起動します。このセクションの手順はすべて、このツールで行います。

1. **すべてのオブジェクトに対する完全同期** 上部の **[コネクタ]** を選択します。前の手順で変更を加えたコネクタ (この場合は Active Directory ドメイン サービス) を特定して選択します。[Actions (操作)] の **[実行]** を選択し、**[Full Synchronization (完全同期)]**、**[OK]** の順に選択します。![Full sync](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)これで、オブジェクトはメタバースで更新されます。次に、メタバースのオブジェクトを確認します。

2. **1 つのオブジェクトに対するプレビューと完全同期** 上部の **[コネクタ]** を選択します。前の手順で変更を加えたコネクタ (この場合は Active Directory ドメイン サービス) を特定して選択します。**[Search Connector Space (コネクタ スペースの検索)]** を選択します。スコープを使用して、変更のテストに使用するオブジェクトを検索します。オブジェクトを選択し、**[プレビュー]** をクリックします。新しい画面で、**[Commit Preview (プレビューのコミット)]** を選択します。![Commit preview](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png) これで、変更はメタバースにコミットされました。

**メタバースのオブジェクトを確認する** ここでは、値が要求されていることと規則が適用されたことを確認するために、いくつかのサンプル オブジェクトを選択します。上部の **[Metaverse Search (メタバース検索)]** を選択します。関連するオブジェクトを検索するために必要なフィルターを追加します。検索結果からオブジェクトを開きます。属性値を確認し、**[同期規則]** 列で、規則が適切に適用されていることを確認します。![Metaverse search](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)
### スケジューラを有効にする
すべてが想定どおりになったら、もう一度スケジューラを有効にすることができます。PowerShell から、`Set-ADSyncScheduler -SyncCycleEnabled $true` を実行します。

## その他の一般的な属性フローの変更
前のセクションでは、属性フローを変更する方法について説明しました。このセクションでは、いくつかの例を紹介します。同期規則の作成方法に関する手順は省略しますが、前のセクションで詳しい手順を確認できます。

### 既定以外の属性の使用
Fabrikam には、地域ごとの特殊文字を名 (given name)、姓 (surname)、表示名 (display name) に用いたフォレストが存在します。それらの属性については、対応するラテン文字表現が拡張属性で見つかります。Azure AD や Office 365 にグローバル アドレス一覧を作成する際は、それらの属性を使用するのが一般的です。

既定の構成では、ローカル フォレストから取得されたオブジェクトが次のように表示されます。![Attribute flow 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

他の属性フローで規則を作成するには、次の手順に従います。

- [スタート] メニューから、**同期規則エディター**を起動します。
- 左側で **[受信]** を選択状態にしたまま、**[Add new rule (新しい規則の追加)]** ボタンをクリックします。
- 規則の名前と説明を入力します。オンプレミスの Active Directory と該当するオブジェクトの種類を選択します。**[リンクの種類]** で **[結合]** を選択します。優先順位には、他の規則で使われていない数値を選びます。標準の規則は 100 から始まるので、この例では 50 を使用できます。![Attribute flow 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
- スコープは空に (つまり、フォレスト内のすべてのユーザー オブジェクトに適用) します。
- 結合規則は空のままに (つまり、標準の規則で結合を処理) します。
- [Transformations (変換)] で、次のフローを作成します。![Attribute flow 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
- **[追加]** をクリックして規則を保存します。
- **Synchronization Service Manager** に移動します。**[コネクタ]** で、先ほど規則を追加したコネクタを選択します。**[実行]** を選択し、**[Full Synchronization (完全同期)]** を選択します。完全同期により、現在の規則を使ってすべてのオブジェクトが再計算されます。

このカスタム規則を同じオブジェクトに適用した後の結果は次のとおりです。![Attribute flow 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### 属性の長さ
文字列属性は、既定では、インデックス可能で、最大長は 448 文字に設定されています。それより多い文字を含む可能性がある文字列属性を使用する場合は、属性フローに次の式を含めるようにします。`attributeName` <- `Left([attributeName],448)`

### userPrincipalSuffix の変更
Active Directory の userPrincipalName 属性は、常にユーザーに認識されるわけではなく、サインイン ID として適切でない場合があります。Azure AD Connect Sync のインストール ウィザードを使用すると、mail など別の属性を選択できます。ただし、場合によっては、属性を計算する必要があります。たとえば、Contoso 社に 2 つの Azure AD ディレクトリがあり、一方は運用環境用、もう一方はテスト用であるとします。テスト テナント内のユーザーについて、サインイン ID に含まれる別のサフィックスを使用することを検討しています。`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

この式では、最初のアット マークの左側のすべてを使用し (Word)、固定文字列をそこに連結します。

### 複数値から単一値への変換
Active Directory の一部の属性は、Active Directory ユーザーとコンピューターでは単一値に見えますが、スキーマでは複数値になっています。例として挙げられるのが、説明属性です。`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

この式で属性が値を持つ場合は、属性の最初のアイテムを使用し (Item)、先頭と末尾のスペースを削除して (Trim)、文字列の最初の 448 文字を維持します (Left)。

### 属性をフローしない
このセクションのシナリオの背景情報については、「[属性フローの処理の制御](#control-the-attribute-flow-process)」を参照してください。

属性をフローしない方法は 2 つあります。1 つ目の方法は、インストール ウィザードで使用できます。[選択した属性を削除](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering)できます。このオプションは、以前に属性を同期したことがない場合に使用できます。ただし、この属性を同期した後に、この機能で属性を削除した場合、同期エンジンによるその属性の管理は停止され、既存の値は Azure AD に残ります。

属性の値を削除し、今後はフローが実行されないようにするには、代わりにカスタム規則を作成する必要があります。

Fabrikam では、クラウドと同期する属性の一部が不要であることに気づきました。不要な属性が Azure AD から削除されるようにする必要があります。![Bad Extension Attributes](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

- 新しい受信同期規則を作成し、説明を入力します。![説明](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
- 種類が **Expression** でソースが **AuthoritativeNull** の属性フローを作成します。リテラル **AuthoritativeNull** は、低い優先度の同期規則が値を設定しようとしても、MV の値は空になることを示します。![Transformation for Extension Attributes](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
- 同期規則を保存します。**同期サービス**を開始し、[コネクタ] を探して **[実行]**、**[完全同期]** の順に選択します。この手順では、すべての属性フローが再計算されます。
- コネクタ スペースを検索して、目的の変更がエクスポート対象になっていることを確認します。![段階的な削除](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## 先進的概念

### 属性フローの処理の制御
複数の受信同期規則が同じメタバース属性に提供されるよう構成されると、その後、優先順位は、優先される規則を決定するために使用されます。優先順位の最も高い (数値が最も小さい) 同期規則は値を提供します。送信規則についても同じことになります。優先順位の最も高い同期規則が優先され、接続されたディレクトリに値を提供します。

場合によっては、同期規則は、値を提供するのではなく、他の規則の動作を決定する必要があります。この例では、いくつかの特殊なリテラルが使用されています。

受信同期規則の場合、リテラルの **NULL** を使用すると、提供する値がフローにないことを示すことができます。優先順位の低い別の規則は値を提供できます。値を提供するルールがない場合、メタバース属性は削除されます。送信規則では、すべての同期規則が処理された後の最終的な値が **NULL** が場合、接続されたディレクトリからこの値が削除されます。

リテラル **AuthoritativeNull** は **NULL** に似ていますが、優先順位の低い規則は値を提供できないという点が異なります。

また、属性フローでは **IgnoreThisFlow** も使用できます。これは、提供するものがないことを示す意味では NULL に似ています。違いは、ターゲットに既に存在する値を削除しないことです。属性フローがそこにあったことがないようなものです。

たとえば次のようになります。

*Out to AD - User Exchange hybrid* では、次のフローが見つかります。`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)` この式は、ユーザーのメールボックスが Azure AD にある場合に属性を Azure AD から AD へフローするという意味になります。Azure AD にない場合は、Active Directory に何もフローされません。この場合、既存の値が AD で保持されます。

### ImportedValue
ImportedValue 関数は、属性名を角かっこではなく引用符で囲む必要がある点で、他のすべての関数とは異なっています。次に例を示します。`ImportedValue("proxyAddresses")`

通常、同期する際は、まだエクスポートされていない場合であっても、エクスポート中にエラーが発生した場合であっても、属性では予想される値を使用します ("top of the tower")。受信同期では、接続されたディレクトリにまだ届いていない属性も最終的には届くと見なされます。また、接続されたディレクトリで確認された値のみを同期することが重要です ("hologram and delta import tower")。

この関数の例は、標準の同期規則 *In from AD – User Common from Exchange* にあります。ハイブリッド Exchange の場合、Exchange Online によって追加された値を同期する必要があるのは、その値が正常エクスポートされたことが確認されたときのみです。`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## 次のステップ

属性フローで使用される[宣言型プロビジョニングの式](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)に関するページをご覧ください。

[Azure AD Connect Sync](active-directory-aadconnectsync-whatis.md) の構成に関するページをご覧ください。

「[オンプレミスの ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

<!---HONumber=AcomDC_0824_2016-->
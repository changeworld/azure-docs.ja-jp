<properties
	pageTitle="PowerApps Enterprise の説明と、使用を開始する方法 | Microsoft Azure"
	description="PowerApps Enterprise の使用を開始し、アプリ サービス環境を作成する"
	services=""
    suite="powerapps"
	documentationCenter=""
	authors="linhtranms"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/29/2015"
   ms.author="litran"/>

# Microsoft PowerApps Enterprise とは?

Microsoft PowerApps Enterprise は、新しい Microsoft Azure サービスです。PowerApps Enterprise では、企業内のビジネス ユーザー向けにモバイル アプリの作成機能を強化し、IT 管理者がこれらのアプリを厳密に管理できるようにしています。

リボンや Excel の数式が組み込まれた Office スタイルのインターフェイスを使用することで、ビジネス ユーザーは次のような機能を持つアプリを作成できます。

- Excel の場合と同様に、折れ線グラフ、円グラフ、横棒グラフを使用してデータを表示する。
- ボタン、テキストの挿入機能、日付の書式設定機能を含むユーザー インターフェイスを作成する。
- リスト ボックス、ドロップダウン リスト、およびオプション ボタンなどの複数の選選択コントロールを追加する。
- イメージのアップロード、写真の撮影、オーディオ/ビデオ ファイルの再生を行う。
- Excel や SQL Server などの ’バックエンド’ システムを使用して情報を表示および更新する。
- Twitter や SharePoint などの PaaS プログラムに接続するアプリに、ビルド済み App Service コネクタを追加する。

IT 管理者は、企業内のビジネス ユーザーによって作成されたアプリを次のように管理できます。

- 作成されたアプリの管理、およびアプリへのユーザー アクセスの管理を行う。
- API を作成し、さまざまなデータ ソースへの接続を作成する
- API に対するユーザー アクセスを管理して、これらのデータ ソースへの接続を管理する。

## 開始するには?

まず、新しい Azure Active Directory (Azure AD) テナントを作成する必要があるかどうかを判定します。AD テナントを既に所有している場合は、Azure ポータルで PowerApps Enterprise を有効にし、API および接続を追加して管理を開始するだけです (このトピックの*手順 2.* から始める)。

AD テナントをまだ所有していない場合は、新しい AD テナントを作成し、Azure ポータルでPowerApps Enterprise を有効にし、API と接続を追加し、管理を開始します。

このトピックでは詳細な手順を示します。

## 手順 1: 新しい Azure AD テナントを作成する、または既存の Azure AD テナントを使用する

PowerApps Enterprise の使用を開始するには、Azure Active Directory (Azure AD) テナントが必要です。テナントは、Azure AD サービスの専用のインスタンスです。

組織または企業が Microsoft Intune または Office 365 などの Microsoft Azure クラウド サービスにサインインすると、組織は自動的に AD テナントを受信し所有するようになります。各 AD テナントは、特異なものであり他の Azure AD テナントとは分離されます。

次の手順を使用して、テナントを既に所有しているかどうかを判定するか、または新しいテナントを作成する方法を決定します。

#### 既存の Office 365 サブスクリプションを所有している
Office 365 サブスクリプション (または Microsoft Dynamic CRM Online、Enterprise Mobility Suite、またはその他の Microsoft サービス) を既に所有している場合は、Azure Active Directory への無料のサブスクリプションが提供されます。Azure AD を使用して、ユーザー アカウントとグループ アカウントを作成し管理することができます。Azure ポータルにサインインできない場合は、おそらくサブスクリプションをアクティブにする必要があります。それを行うには、[Azure クラシック ポータル]( https://manage.windowsazure.com/)に進み、1 回限りの登録処理を実行します。次の[手順]( https://technet.microsoft.com/library/dn832618.aspx)を使用して、Azure AD テナントにアクセスします。

#### Microsoft アカウントに関連付けられた既存の Azure サブスクリプションを所有している
以前に個人の Microsoft アカウント (hotmail または live) で Azure サブスクリプションにサインアップしている場合は、既にテナントを所有しています。 [Azure クラシック ポータル]( https://manage.windowsazure.com/)の **[すべてのアイテム]** と **[Active Directory]** に**既定のテナント**が表示されます。このテナントは自由に使用できますが、組織の管理者アカウントを作成する必要がある場合があります。

そのためには、次の手順を実行してください。または、新しいテナントを作成し、同様のプロセスでそのテナントに管理者を作成することもできます。

1.	個人アカウントで [Azure クラシック管理ポータル]( https://manage.windowsazure.com/)にサインインします。
2.	左側のメニュー バーにある **[Active Directory]** を選択します。
3.	使用可能なディレクトリの一覧で、**[既定のディレクトリ]** を選択します。
4.	上部にある **[ユーザー]** タブをクリックします。1 人のユーザーが [ソース ディレクトリ] 列に “Microsoft アカウント” と共に一覧表示されます。
5.	下部にある **[ユーザーの追加]** を選択します。
6.	**[ユーザーの追加]** フォームで、次の詳細を指定します。  

	プロパティ | 説明
--- | ---
ユーザーの種類 | 組織内の新しいユーザーです。
ユーザー名 | この管理者のユーザー名を選択します。
名/姓/表示名 | 値を入力します。
ロール | グローバル管理者
連絡用メール アドレス | 値を入力します。
省略可能。 | Multi-Factor Authentication を有効にします。  

	**[作成]** ボタンを選択して完了させ、一時的なパスワードを表示します。

終了したら、新しい管理ユーザー用の一時的なパスワードを記録します。一時的なパスワードを変更するには、この新しいユーザー アカウントで [https://login.microsoftonline.com]( https://login.microsoftonline.com) にサインインし、パスワードを変更します。連絡用電子メールを使用して、ユーザーにパスワードを直接送信することもできます。


#### 組織のアカウントに関連付けられた既存の Azure サブスクリプションを所有している
以前に組織のアカウントで Azure サブスクリプションにサインアップしている場合は、既にテナントを所有しています。[Azure クラシック ポータル]( https://manage.windowsazure.com/)の **[すべてのアイテム]** と **[Active Directory]** にテナントが表示されます。必要に応じて、このテナントを自由に使用できます。下部のタスク バーにある **[新規]** メニューを使用して新しいテナントを作成することもできます。

#### 上記のいずれも所有しておらず、最初から始める
上記のいずれにも該当しない場合は、[https://account.windowsazure.com/organization]( https://account.windowsazure.com/organization) にアクセスして、新しい組織で Azure にサインアップします。サインアップすると、選択したドメイン名が付けられた独自の Azure AD テナントを取得できます。[Azure クラシック ポータル]( https://manage.windowsazure.com/)の左側のメニューの **[Active Directory]** にテナントが表示されます。

## 手順 2: 新しい Azure サブスクリプションを作成するか、または既存のものを使用する
AD テナントが用意できましたので、次に新しい Azure サブスクリプションを作成することも、既存のものを使用することもできます。Azure AD サブスクリプションには複数のエディションがあります。PowerApps Enterprise の場合は、Free エディションを使用することができます。ただし、AAD プロキシを使用して、オンプレミスのデータへのハイブリッド接続を作成する必要がある場合は、Basic エディションまたは Premium エディションが必要です。

詳細については、「[Azure Active Directory のエディション](../active-directory/active-directory-editions.md)」を参照してください。


## 手順 3: Azure 作業サブスクリプションで PowerApps Enterprise にサインアップする
> [AZURE.NOTE] 次の手順では、サブスクリプションの管理者が Azure ポータルにサインインし、要求を送信する必要があります。

AD テナントと Azure サブスクリプションが準備できましたので、作業サブスクリプションの管理者は次に PowerApps Enterprise にサインアップできます。管理者はまた、社内のユーザーを '管理' PowerApps に追加し (ユーザーにアクセス許可を与えることも含む)、Azure サブスクリプションに公開された PowerApps を管理することができます。

PowerApps Enterprise にサインアップしていない状態で、[Azure ポータル]( https://portal.azure.com/)にアクセスして PowerApps を参照しても、アクセス ブレードは表示されません。会社をサインアップする場合、**サブスクリプション管理者**は [PowerApps](http://go.microsoft.com/fwlink/p/?LinkId=716848) にアクセスすることで、料金やサインアップ プロセスの詳細をお問い合わせいただけます。

![][4]

サインアップ プロセスを完了し、PowerApps Enterprise を使用する準備が整ったら、次のことができるようになります。

- 社内のユーザーを追加し、[ロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)を使用して、これらのユーザーに、PowerApps Enterprise ポータルにアクセスするための PowerApps Admin ロールを付与します。
- PowerApps をホストする専用のアプリ サービス環境を作成します。
- 専用のアプリ サービス環境内で実行される API と接続を作成します。
- PowerApps で作成したアプリだけでなく、Web アプリ、モバイル アプリ、API アプリ、ロジック アプリなどのアプリもアプリ サービス環境に追加することができます。

次の例では、Contoso 社が PowerApps にサインアップしています。この新しい **[PowerApps]** ブレードでは、このアプリ サービス環境を使用して作成したさまざまな種類のアプリの要約を確認できます。**[API の管理]** では、Microsoft で作成された API の要約 ([マイクロソフト管理]) と Contoso で作成された API ([IT 管理]) を確認できます。

![企業の PowerApps ブレードのサンプル][3]


## 手順 4: アプリ サービス環境を作成する
PowerApps の API と接続に加えて、モバイル アプリ、Web アプリ、API アプリ、およびロジック アプリをホストするアプリ サービス環境を作成します。

アプリ サービス環境は、すべてのアプリを安全に実行するための分離された専用環境です。コンピューティング リソースは、アプリ サービス環境ごとに有効になり、お使いのアプリを実行するためだけに使用されます。PowerApps Enterprise にサインアップすると、アプリで使用される API と接続は専用のアプリ サービス環境を使用してホストされます。このアプリ サービス環境は、"特殊な" タイプのアプリ サービス環境です。具体的には次の処理が行われます。

- このアプリ サービス環境は、任意の目的で使用できます。これは、サブスクリプションではなく、会社に関連付けられます。
- PowerApps で作成したアプリで使用されるように、API と接続を構成します。ただし、前述アプリ サービス環境には、Web アプリ、モバイル アプリ、ロジック アプリ、および API アプリを追加することもできます。
- 課金は固定で、PowerApps Enterprise に含められます。  
- スケールは自動的に管理されます。追加のコンピューティング リソースが必要なかどうかを判断するために、環境を監視する必要はありません。

通常の Azure アプリ サービス環境にはさまざまな機能があります。詳細については、「[App Service Environment の概要](../app-service-web/app-service-app-service-environment-intro.md)」を参照してください。

#### 使用を開始するための前提要件

- Azure の企業サブスクリプション
- 社内のサブスクリプション管理者が会社で[PowerApps](powerapps-get-started-azure-portal.md) Enterprise にサインアップしている。
- PowerApps 管理者 (PowerApps の "所有者") またはサブスクリプション管理者として Azure ポータルにサインインしている。

### アプリ サービス環境を作成する
> [AZURE.NOTE] アプリ サービス環境を作成するオプションが表示されない場合、テナントのアプリ サービス環境は既に作成されています。詳細を表示するには、**[設定]** を選択してアプリ サービス環境を開きます。

1. [Azure ポータル]( https://portal.azure.com/)で、職場アカウントでサインインします。たとえば、*yourUserName*@*YourCompany*.com でサインインします。これにより、会社のサブスクリプションに自動的にサインインされます。

2. タスク バーの **[参照]** をクリックします。

	![PowerApps の参照][1]

3. 一覧で、PowerApps が見つかるまでスクロールするか、「*powerapps*」と入力します。

	![PowerApps の検索][2]

4. **[PowerApps]** ブレードで、**[App Service Environment を作成して使用を開始する]** を選択するか、*[設定]* の **[App Service Environment ]** を選択します。

	![][5]

	> [AZURE.NOTE] **[App Service Environment を作成して使用を開始する]** をクリックすると、App Service Environment の詳細を示すブレードが追加で表示されます。そのブレード上で作成リンクをクリックするだけで、作成ブレードが起動します。

5. 次に、名前を入力し、使用するサブスクリプションを選択し、新しいリソース グループを選択または作成し、仮想ネットワークを選択します。仮想ネットワークは、選択するとその後変更できなくなるので**注意してください**。

	![][6] 

App Service Environmentでの仮想ネットワークの動作の詳細については、「[App Service Environment の作成方法](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)」を参照してください。

6. **[追加]** を選択して、アプリ サービス環境の作成を完了します。

> [AZURE.TIP] PowerApps を使用してアプリ サービス環境を作成する場合は、コンピューティング リソース プールを構成するよう求められません。この手順は自動的に処理されます。

このアプリ サービス環境には、Web アプリ、モバイル アプリ、API アプリを追加することもできます。実際には、App Service Environment でサポートされている任意のものを追加するための環境です。

### App Service Environment を管理する管理者を追加します。

アプリ サービス環境にアクセスするには、API や接続などのリソースを作成し、ユーザーを所有者ロールで追加する必要があります。

1. 先ほど作成したアプリ サービス環境を選択します。
2. [Essentials] で、**[リソース グループ]** プロパティを選択します。これにより、アプリ サービス環境を含むリソース グループが開きます。![][7]
3. RBAC アイコンを選択してアクセス許可を管理します。![][8]ユーザーの追加やロールの割り当ては、Azure 内で[ロールベースのアクセス制御]( https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)を使用する場合と似ています。

> [AZURE.NOTE] 現時点で、アプリ サービス環境に RBAC アクセス許可を付与することはできません。親のリソース グループ レベルでは RBAC アクセス許可を付与することができます。

## まとめと次のステップ
これで会社は PowerApps にサインアップし、アプリ サービス環境を取得しました。次に、アプリで使用可能な API と接続を追加することができます。

- [PowerApps アプリを監視する](powerapps-manage-monitor-usage.md)
- [PowerApps のための API を開発する](powerapps-develop-api.md)
- [新しい API を追加し、接続を追加し、ユーザーにアクセスを許可する](powerapps-manage-api-connection-user-access.md)
- [既存の API とそのプロパティを更新する](powerapps-configure-apis.md)


[1]: ./media/powerapps-get-started-azure-portal/browseall.png
[2]: ./media/powerapps-get-started-azure-portal/allresources.png
[3]: ./media/powerapps-get-started-azure-portal/powerappsblade.png
[4]: ./media/powerapps-get-started-azure-portal/noaccess.png
[5]: ./media/powerapps-get-started-azure-portal/createase.png
[6]: ./media/powerapps-get-started-azure-portal/aseproperties.png
[7]: ./media/powerapps-get-started-azure-portal/aseessentials.png
[8]: ./media/powerapps-get-started-azure-portal/resourcegrouprbac.png

<!-----HONumber=AcomDC_0413_2016-->
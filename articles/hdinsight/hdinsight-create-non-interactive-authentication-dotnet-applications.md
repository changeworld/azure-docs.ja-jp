<properties
	pageTitle="非対話型認証 .NET HDInsight アプリケーションを作成する | Microsoft Azure"
	description="非対話型認証 .NET HDInsight アプリケーションを作成する方法について説明します。"
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/23/2016"
	ms.author="jgao"/>

# 非対話型認証 .NET HDInsight アプリケーションを作成する

アプリケーション独自の ID (非対話型) またはアプリケーションのサインイン ユーザーの ID (対話型) のいずれかで、.NET Azure HDInsight アプリケーションを実行できます。対話型アプリケーションの例については、「[HDInsight .NET SDK を使用して Hive/Pig/Sqoop ジョブを送信する](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk)」を参照してください。この記事では、Azure HDInsight に接続し、Hive ジョブを送信する非対話型認証 .NET アプリケーションを作成する方法について説明します。

.NET アプリケーションには、以下が必要です。

- Azure サブスクリプションのテナント ID
- Azure Directory アプリケーションのクライアント ID
- Azure Directory アプリケーションのシークレット キー  

メイン プロセスには、次の手順が含まれます。

2. Azure Directory アプリケーションを作成します。
2. AD アプリケーションにロールを割り当てます。
3. クライアント アプリケーションを実行します。


##前提条件

- HDInsight クラスター。[入門のチュートリアル](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster)に記載されている手順を参照して作成できます。 




## Azure Directory アプリケーションを作成する 
Active Directory アプリケーションを作成すると、アプリケーションとサービス プリンシパルの両方が作成されます。このアプリケーションは、アプリケーションの ID を使用して実行できます。

現時点では、新しい Active Directory アプリケーションを作成するには、Azure クラシック ポータルを使用する必要があります。この機能は、今後のリリースで、Azure ポータルに追加されます。これらの手順は、Azure Powershell または Azure CLI を介して実行することもできます。サービス プリンシパルで PowerShell または CLI を使用する方法については、「[Azure Resource Manager でのサービス プリンシパルの認証](../resource-group-authenticate-service-principal.md)」を参照してください。

**Azure Directory アプリケーションを作成するには**

1.	[Azure クラシック ポータル](https://manage.windowsazure.com/)にサインインします。
2.	左側のペインで **[Active Directory]** を選択します。

    ![Azure クラシック ポータルの Active Directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\active-directory.png)
    
3.	新しいアプリケーションを作成するために使用するディレクトリを選択します。既存のディレクトリを選択する必要があります。
4.	上部の **[アプリケーション]** をクリックすると、既存のアプリケーション一覧が表示されます。
5.	下部の **[追加]** をクリックし、新しいアプリケーションを追加します。
6.	**[名前]** を入力し、**[Web アプリケーションや Web API]** を選択して、**[次へ]** をクリックします。

    ![新しい Azure Active Directory アプリケーション](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application.png)

7.	**[サインオン URL]** と **[アプリケーション ID/URI]** を入力します。**[サインオン URL]** には、アプリケーションについて説明する web サイトの URI を指定します。Web サイトの存在は検証されません。[アプリケーション ID/URI] には、アプリケーションを識別する URI を指定します。次に、**[完了]** をクリックします。アプリケーションが作成されるまでしばらく待ちます。アプリケーションが作成されると、ポータルに新しいアプリケーションの [概要] ページが表示されます。ポータルを閉じないでください。

    ![新しい Azure Active Directory アプリケーションのプロパティ](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application-properties.png)

**アプリケーション クライアント ID とシークレット キーを取得するには**

1.	新しく作成した AD アプリケーション ページから、上部メニューの **[構成]** をクリックします。
2.	**[クライアント ID]** をコピーします。この ID は .NET アプリケーションで必要になります。
3.	**[キー]** の **[時間の選択]** ドロップダウンをクリックし、**[1 年]** または **[2 年]** を選択します。構成を保存するまで、キー値は表示されません。
4.	ページの下部にある **[保存]** をクリックします。シークレット キーが表示されたら、キーをコピーします。この ID は .NET アプリケーションで必要になります。

##AD アプリケーションをロールに割り当てる

アクションを実行するアクセス許可を付与するには、[ロール](../active-directory/role-based-access-built-in-roles.md)にアプリケーションを割り当てる必要があります。スコープは、サブスクリプション、リソース グループ、またはリソースのレベルで設定できます。アクセス許可は、スコープの下位レベルに継承されます (たとえば、アプリケーションをリソース グループの閲覧者ロールに追加すると、アプリケーションではリソース グループとそれに含まれているすべてのリソースを読み取ることができます)。このチュートリアルでは、リソース グループ レベルでスコープを設定します。Azure クラシック ポータルはリソース グループをサポートしていないので、この部分は Azure ポータルで実行する必要があります。

**AD アプリケーションに所有者ロールを追加するには**

1.	[Azure ポータル](https://portal.azure.com)にサインインします。
2.	左側のウィンドウの **[リソース グループ]** をクリックします。
3.	HDInsight クラスター (このチュートリアルの後半で Hive クエリを実行するクラスター) を含むリソース グループをクリックします。リソース グループ数が多すぎる場合は、フィルターを使用することができます。
4.	クラスター ブレードから **[アクセス]** をクリックします。

    ![雲と雷のアイコン = クイック スタート](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)
5.	**[ユーザー]** ブレードから **[追加]** をクリックします。
6.	指示に従って、前の手順で作成した AD アプリケーションに**所有者**ロールを追加します。正常に完了し、所有者ロールで [ユーザー] ブレードを見ると、そのアプリケーションが表示されます。


##HDInsight クライアント アプリケーションを開発する

[HDInsight での Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically/#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk)に関するページの指示に従って、C# .NET コンソール アプリケーションを作成します。次に、GetTokenCloudCredentials メソッドを次のように置き換えます。

    public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
    {
        var authFactory = new AuthenticationFactory();

        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };

        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

        var accessToken =
            authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never)
                .AccessToken;

        return new TokenCloudCredentials(accessToken);
    }

PowerShell 経由でテナント ID を取得するには:

    Get-AzureRmSubscription

または、次の Azure CLI を使います。

    azure account show --json

      
## 関連項目

- [HDInsight での Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md)
- [ポータルを利用し、Active Directory のアプリケーションとサービス プリンシパルを作成する](../resource-group-create-service-principal-portal.md)
- [Azure Resource Manager でサービス プリンシパルを認証する](../resource-group-authenticate-service-principal.md)
- [Azure のロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)

<!---HONumber=AcomDC_0330_2016-->
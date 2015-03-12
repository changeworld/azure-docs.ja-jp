<properties 
	pageTitle="Hudson で Azure BLOB サービスを使用する方法 | Microsoft Azure" 
	description="Hudson で Azure BLOB ストレージをビルド アーティファクトのリポジトリとして使用する方法について説明します。" 
	services="storage" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="robmcm"/>

#Hudson 継続的インテグレーション ソリューションでの Azure ストレージの使用

*[Microsoft Open Technologies Inc.][ms-open-tech]*

ここでは、Hudson 継続的インテグレーション (CI) ソリューションで作成されるビルド アーティファクトのリポジトリとして、または、ビルド プロセスで使用されるダウンロード可能なファイルのソースとして Azure BLOB サービスを使用する方法について説明します。この方法が有用になるシナリオの 1 つが、アジャイル開発環境で (Java などの言語を使って) コーディングをしており、継続的インテグレーションに基づいてビルドを実行するとき、ビルド アーティファクト用のリポジトリが必要な場合です。このリポジトリがあれば、ビルド アーティファクトを他の組織のメンバーや顧客と共有したり、そのアーカイブを保存したりできます。もう 1 つのシナリオとしては、ビルド ジョブ自体にその他のファイルが必要になる場合、たとえば、ビルドの入力で依存関係のダウンロードが必要になる場合などが考えられます。

このチュートリアルでは、Microsoft Open Technologies, Inc が公開している Hudson CI 用の Azure ストレージ プラグインを使用します。

## 目次

-   [Hudson の概要][]
-   [BLOB サービスを使用するメリット][]
-   [前提条件][]
-   [Hudson CI で BLOB サービスを使用する方法][]
-   [Azure ストレージ プラグインのインストール方法][]
-   [Azure Storage プラグインを構成してストレージ アカウントを使用する方法][]
-   [ビルド後にビルド アーティファクトをストレージ アカウントにアップロードするアクションの作成方法][]
-   [BLOB ストレージからのダウンロードを実行するビルド手順の作成方法][]
-   [BLOB サービスが使用するコンポーネント][]

## <a id="overview"></a>Hudson の概要 ##
Hudson では、開発者がコードの変更を簡単に統合し、ビルドを自動的に頻繁に生成できるようになり、ソフトウェア プロジェクトの継続的な統合が実現されます。これにより、開発者の生産性が向上します。ビルドはバージョン管理され、ビルド アーティファクトをさまざまなリポジトリにアップロードできます。このトピックでは、Azure BLOB ストレージをビルド アーティファクトのリポジトリとして使用する方法について説明します。また、Azure BLOB ストレージから依存関係をダウンロードする方法も紹介します。

Hudson の詳細については、「[Meet Hudson (Hudson について)][]」を参照してください。

## <a id="benefits"></a>BLOB サービスを使用するメリット ##

BLOB サービスを使用してアジャイル開発のビルド アーティファクトをホストするメリットには、次の点が挙げられます。

- ビルド アーティファクトやダウンロード可能な依存関係に高可用性を実現。
- Hudson CI ソリューションでビルド アーティファクトをアップロードする際のパフォーマンスを改善。
- 顧客およびパートナーがビルド アーティファクトをダウンロードする際のパフォーマンスを改善。
- 匿名アクセス、有効期限ベースの共有アクセス署名によるアクセス、プライベート アクセスなどから 1 つを選んでユーザー アクセス ポリシーを制御。

## <a id="prerequisites"></a>前提条件 ##

Hudson CI ソリューションで BLOB サービスを使用するには、次のものが必要です。

- Hudson 継続的インテグレーション ソリューション。

    Hudson CI ソリューションがない場合には、次の方法によって Hudson CI ソリューションを実行できます。

    1. Java が有効なコンピューターで、<http://hudson-ci.org/> から Hudson WAR をダウンロードします。
    2. コマンド プロンプトを開いて Hudson WAR が格納されているフォルダーに移動し、Hudson WAR を実行します。たとえば、version 3.1.2 をダウンロードした場合は、次のコマンドを実行します。

        `java -jar hudson-3.1.2.war`

    3. ブラウザーで `http://localhost:8080/` を開きます。これで Hudson ダッシュボードが開かれます。

    4. Hudson を最初に使用するときに、`http://localhost:8080/` で初期セットアップを完了します。 

    5. 初期セットアップの完了後、Hudson WAR の実行中のインスタンスを取り消し、Hudson WAR を再度起動します。次に、Azure ストレージ プラグインのインストールと構成を行うための Hudson ダッシュボード (`http://localhost:8080/`) を再度開きます。

        通常の Hudson CI ソリューションであればサービスとして実行されるように設定しますが、このチュートリアルではコマンド ラインで Hudson war を実行するだけで十分です。

- Azure アカウント。Azure アカウントにサインアップできます。 <http://www.windowsazure.com>。

- Azure ストレージ アカウント。まだストレージ アカウントを取得していない場合には、[ストレージ アカウントの作成方法][]を示した記事の手順に従って作成できます。

- 以降では、Hudson CI のビルド アーティファクトで BLOB サービスをリポジトリとして使用するうえで必要な手順を、基本的な例を使って説明しています。Hudson CI ソリューションにある程度習熟していることが望ましいものの、必須ではありません。

## <a id="howtouse"></a>Hudson CI で BLOB サービスを使用する方法 ##

Hudson で BLOB サービスを使用するには、Azure ストレージ プラグインをインストールし、そのプラグインを構成してストレージ アカウントを使用するようにしたうえで、ビルド後にビルド アーティファクトをストレージ アカウントにアップロードするアクションを作成する必要があります。以降のセクションでは、ここに挙げた手順について説明します。

## <a id="howtoinstall"></a>Azure ストレージ プラグインのインストール方法 ##

1. Hudson ダッシュボードで、**[Manage Hudson]** をクリックします。
2. **[Manage Hudson]** ページで **[Manage Plugins]** をクリックします。
3. **[Available]** タブをクリックします。
4. **[Others]** をクリックします。
5. **[Artifact Uploaders]** セクションで、**Microsoft Azure Storage プラグイン**を確認します。
6. **[Install]** をクリックします。
7. インストール完了後、Hudson を再起動します。

## <a id="howtoconfigure"></a>Azure Storage プラグインを構成してストレージ アカウントを使用する方法 ##

1. Hudson ダッシュボードで、**[Manage Hudson]** をクリックします。
2. **[Manage Hudson]** ページで **[Configure System]** をクリックします。
3. **[Microsoft Azure Storage Account Configuration]** セクションで、次の操作を行います。
    1. Azure ポータル (<https://manage.windowsazure.com>) で取得したストレージ アカウント名を入力します。
    2. 同様に、Azure ポータルで取得したストレージ アカウント キーを入力します。
    3. パブリック Azure クラウドを使用している場合、**[Blob Service Endpoint URL]** には既定値を使用します。これとは異なる Azure クラウドを使用している場合には、Azure 管理ポータルでストレージ アカウント用に指定されたエンドポイントを使用します。 
    4. **[Validate storage credentials]** をクリックしてストレージ アカウントを検証します。 
    5. [省略可能] Hudson CI で利用できるストレージ アカウントを追加する場合には、**[Add more storage accounts]** をクリックします。
    6. **[Save]** をクリックして設定を保存します。

## <a id="howtocreatepostbuild"></a>ビルド後にビルド アーティファクトをストレージ アカウントにアップロードするアクションの作成方法 ##

説明のため、ストレージ アカウントにファイルをアップロードするためのビルド後のアクションを追加する前に、複数のファイルを作成するジョブを作成する必要があります。

1. Hudson ダッシュボードで、**[New Job]** をクリックします。
2. ジョブの名前を **MyJob** に設定し、**[Build a free-style software job]** をクリックして、**[OK]** をクリックします。
3. ジョブ構成の **[Build]** セクションで **[Add build step]** をクリックした後、**[Execute Windows batch command]** を選択します。
4. **[Command]** で次のコマンドを使用します。

        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
 
5. ジョブ構成の **[Post-build Actions]** セクションで **[Upload artifacts to Microsoft Azure Blob storage]** をクリックします。
6. **[Storage Account Name]** では、使用するストレージ アカウントを選択します。
7. **[Container Name]** では、コンテナー名を指定します(コンテナーは、ビルド アーティファクトをアップロードする時点で存在していなければ、自動で作成されます)。環境変数を使用することもできます。この例では、コンテナー名に「**${JOB_NAME}**」と入力します。

    **ヒント**
    
    **[Execute Windows batch command]** にスクリプトを入力した **[Command]** セクションの下には、Hudson が認識できる環境変数へのリンクがあります。環境変数の名前および説明を確認するには、リンクをクリックします。**BUILD_URL** など、特殊文字が含まれる環境変数は、コンテナー名および共通仮想パスに使用できません。

8. この例では **[Make new container public by default]** をクリックします。(プライベート コンテナーを使用する場合には、共有アクセス署名を作成してアクセスを許可する必要があります。ただし、この点についてはこのトピックでは取り扱いません。共有アクセス署名に関する詳細については、[共有アクセス署名の作成に関するページ](http://go.microsoft.com/fwlink/?LinkId=279889)を参照してください)。
9. [省略可能] ビルド アーティファクトをアップロードする前にコンテナーの内容をクリアする場合、**[Clean container before uploading]** をクリックします (コンテナーの内容をクリアしない場合は、チェック ボックスをオフにします)。
10. **[List of Artifacts to upload]** では、「**text/*.txt**」と入力します。
11. **[Common virtual path for uploaded artifacts]** では、「**${BUILD\_ID}/${BUILD\_NUMBER}**」と入力します。
12. **[Save]** をクリックして設定を保存します。
13. Hudson ダッシュボードで、**[Build Now]** をクリックして **MyJob** を実行します。コンソール出力でステータスを確認します。ビルド後のアクションによってビルド アーティファクトのアップロードが開始されると、コンソール出力に Azure ストレージに関するステータス メッセージが表示されます。
14. ジョブが正常に完了すると、パブリック BLOB を開いてビルド アーティファクトを確認できます。
    1. Azure 管理ポータルにログインします。 <https://manage.windowsazure.com>。
    2. **[ストレージ]** をクリックします。
    3. Hudson に使用したストレージ アカウント名をクリックします。
    4. **[コンテナー]** をクリックします。
    5. **myjob** という名前のコンテナーをクリックします。これは、Hudson ジョブを作成したときに割り当てたジョブ名を小文字にしたものです。Azure ストレージでは、コンテナー名と BLOB 名は小文字です (大文字と小文字は区別されます)。**myjob** という名前のコンテナーの BLOB の一覧に、**hello.txt** と **date.txt** の 2 つがあります。そのどちらかの URL をコピーして、ブラウザーで開きます。このテキスト ファイルがビルド アーティファクトとしてアップロードされていることがわかります。

アーティファクトを Azure BLOB ストレージにアップロードするビルド後のアクションは、ジョブごとに 1 つのみ作成できます。**[List of Artifacts to upload]** でセミコロンを区切り記号として使用することで、アーティファクトを Azure BLOB ストレージにアップロードするビルド後のアクション 1 つに、(ワイルドカードを含む) 複数のファイルとファイル パスを指定できます。たとえば、Hudson ビルドによってワークスペースの **build** フォルダーに JAR ファイルと TXT ファイルが生成され、これら両方のファイルを Azure BLOB ストレージにアップロードする場合、**[List of Artifacts to upload]** の値には「**build/\*.jar;build/\*.txt**」を使用します。また、2 重コロンの構文を使用すると、BLOB 名で使用するパスを指定できます。たとえば、JAR ファイルのアップロードに BLOB パス内の **binaries** を使用し、TXT ファイルのアップロードに BLOB パス内の **notices** を使用する場合、**[List of Artifacts to upload]** の値には「**build/\*.jar::binaries;build/\*.txt::notices**。

## <a name="howtocreatebuildstep"></a>BLOB ストレージからのダウンロードを実行するビルド手順の作成方法 ##

次の手順では、BLOB ストレージから項目をダウンロードするビルド手順を構成する方法を示します。この手順は、Azure BLOB ストレージに保持している JAR ファイルなどの項目をビルドに含める場合などに便利です。

1. ジョブ構成の **[Build]** セクションで **[Add build step]** をクリックし、**[Download from Azure Blob storage]** を選択します。
2. **[Storage Account Name]** では、使用するストレージ アカウントを選択します。
3. **[Container name]** では、ダウンロードする BLOB が格納されているコンテナーの名前を指定します。環境変数を使用できます。
4. **[Blob name]** には BLOB 名を指定します。環境変数を使用できます。また、アスタリスクを使用して、BLOB 名の先頭文字 (複数可) の後にワイルドカードを指定できます。たとえば「**project\***」と入力すると、名前が「**project**」で始まる BLOB がすべて指定されます。
5. [省略可能] **[Download path]** では、Hudson を実行しているコンピューター上のパスを指定します。Azure BLOB ストレージのファイルはこのパスにダウンロードされます。環境変数も使用できます(**[Download path]** に値を入力しない場合、Azure BLOB ストレージのファイルは、ジョブのワークスペースにダウンロードされます)。

Azure BLOB ストレージからダウンロードする項目が他にもある場合は、追加のビルド手順を作成できます。

目的の BLOB が正常にダウンロードされていることを確かめるには、ビルドを実行した後に、ビルド履歴のコンソール出力またはダウンロード先を確認します。 

## <a id="components"></a>BLOB サービスが使用するコンポーネント ##

以下では、BLOB サービス コンポーネントの概要を説明します。

- **ストレージ アカウント**:Azure のストレージにアクセスする場合には必ず、ストレージ アカウントを使用します。これは、アクセスする BLOB の名前空間の中でも最高レベルに位置するものです。アカウントに格納できるコンテナーの数は、コンテナーの合計サイズが 100 TB 未満である限り無制限です。
- **コンテナー**:コンテナーは、BLOB のセットをグループ化します。すべての BLOB はコンテナーに格納されている必要があります。1 つのアカウントに格納できるコンテナーの数は無制限です。また、1 つのコンテナーに保存できる BLOB の数も無制限です。
- **BLOB**:任意の種類およびサイズのファイルです。Azure のストレージ サービスに格納できる BLOB には、ブロック BLOB とページ BLOB の 2 種類があります。ほとんどのファイルはブロック BLOB です。1 つのブロック BLOB には、最大で 200 GB までのデータを格納できます。このチュートリアルでは、ブロック BLOB を使用します。もう 1 つの種類の BLOB であるページ BLOB には、最大 1 TB までのデータを格納できます。ファイルのバイト数の範囲が頻繁に変更される場合には、こちらの方が効率的です。BLOB の詳細については、「[ブロック BLOB およびページ BLOB について]」を参照してください(http://msdn.microsoft.com/library/windowsazure/ee691964.aspx)。
- **URL 形式**:BLOB は、次の URL 形式を使用してアドレスを指定し、アクセスできます。

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
    
    (ここに挙げた形式は、パブリック Azure クラウドに適用されるものです。これとは異なる Azure クラウドを使用している場合には、Azure 管理ポータル内のエンドポイントを使用して URL エンドポイントを指定します。)

    この形式では、 `storageaccount` がストレージ アカウントの名前、 `container_name` がコンテナーの名前、 `blob_name` が BLOB の名前を表します。コンテナー名にはパスを複数使用することができます。その場合には、スラッシュ (**/**) で区切ります。このチュートリアルで例に使用したコンテナー名は **MyJob**、共通仮想パスは **${BUILD\_ID}/${BUILD\_NUMBER}** でした。このため、BLOB の URL は次のようになります。

    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

  [Hudson の概要]: #overview
  [BLOB サービスを使用するメリット]: #benefits
  [前提条件]: #prerequisites
  [Hudson CI で BLOB サービスを使用する方法]: #howtouse
  [Azure ストレージ プラグインのインストール方法]: #howtoinstall
  [Azure Storage プラグインを構成してストレージ アカウントを使用する方法]: #howtoconfigure
  [ビルド後にビルド アーティファクトをストレージ アカウントにアップロードするアクションの作成方法]: #howtocreatepostbuild
  [BLOB ストレージからのダウンロードを実行するビルド手順の作成方法]: #howtocreatebuildstep
  [BLOB サービスが使用するコンポーネント]: #components
  [ストレージ アカウントの作成方法]: http://go.microsoft.com/fwlink/?LinkId=279823
  [Meet Hudson (Hudson について)]: http://wiki.eclipse.org/Hudson-ci/Meet_Hudson
  [ms-open-tech]: http://msopentech.com

<!--HONumber=42-->

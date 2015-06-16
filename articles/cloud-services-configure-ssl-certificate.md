<properties 
	pageTitle="クラウド サービス向けの SSL の構成 - Azure" 
	description="Web ロールの HTTPS エンドポイントを指定する方法および SSL 証明書をアップロードしてアプリケーションを保護する方法を説明します。" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/13/2015" 
	ms.author="adegeo"/>




# Azure でアプリケーションの SSL を構成する

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

Secure Socket Layer (SSL) の暗号化は、インターネットを介して送信されるデータをセキュリティで保護する際に最もよく使用される方法です。この一般的なタスクでは、Web ロールの HTTPS エンドポイントを指定する方法および SSL 証明書をアップロードしてアプリケーションを保護する方法を説明します。

> [AZURE.NOTE]このタスクの手順は、Azure クラウド サービス に適用されます。Web サイトについては、「[Azure Web サイトの SSL 証明書の構成](app-service-web/web-sites-configure-ssl-certificate.md)」を参照してください。

このタスクでは、運用環境の展開を使用します。ステージング環境の展開を使用する場合に関する情報については、このトピックの最後で紹介します。

## 手順 1. SSL 証明書を取得する

アプリケーションの SSL を構成するには、最初に、セキュリティ保護のための証明書を発行する信頼されたサード パーティである、証明機関 (CA) によって署名された SSL 証明書を取得する必要があります。まだ SSL 証明書がない場合は、SSL 証明書を販売する会社から取得する必要があります。

証明書は、Azure における SSL 証明書の次の要件を満たす必要があります。

-   証明書は秘密キーを含む必要があります。
-   証明書はキー交換のために作成され、Personal Information Exchange (.pfx) ファイルにエクスポートできる必要があります。
-   証明書の件名はクラウド サービスへのアクセスに使用されるドメインと一致する必要があります。証明機関 (CA) から cloudapp.net ドメインの SSL 証明書を取得することはできません。サービスにアクセスするときに使用するカスタム ドメイン名を取得する必要があります。CA に証明書を要求するときは、証明書の件名がアプリケーションにアクセスするために使用するカスタム ドメイン名と一致している必要があります。たとえば、カスタム ドメイン名が **contoso.com** である場合は、***.contoso.com** または **www.contoso.com** の証明書を CA に要求します。
-   証明書では、2048 ビット以上の暗号化を使用する必要があります。

テスト目的で、自己署名証明書を作成して使用できます。自己署名証明書は CA を通じて認証されないため、cloudapp.net ドメインを Web サイト URL として使用できます。たとえば、下のタスクでは自己署名証明書を使用しますが、証明書で使用される共通名 (CN) は **sslexample.cloudapp.net** です。IIS マネージャーを使用して自己署名証明書を作成する方法の詳細については、「[Windows Azure のサービス証明書を作成する][]」を参照してください。

次に、この証明書に関する情報を、サービス定義ファイルおよびサービス構成ファイルに含める必要があります。

## ステップ 2: サービス定義ファイルとサービス構成ファイルを変更する

アプリケーションは、証明書を使用するように構成する必要があります。また、HTTPS エンドポイントを追加する必要があります。その結果として、サービス定義ファイルおよびサービス構成ファイルを更新する必要があります。

1.  お使いの開発環境で、サービス定義ファイル (CSDEF) を開き、**WebRole** セクション内に **Certificates** セクションを追加し、証明書に関する次の情報を含めます。

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
							 storeLocation="LocalMachine" 
                    		 storeName="CA" />
            </Certificates>
        ...
        </WebRole>

    **Certificates** セクションでは、証明書の名前、場所、およびこの証明書があるストアの名前を定義します。CA (証明機関) ストアにこの証明書を保存することを選択しましたが、その他のオプションを選択することもできます。詳細については、「[サービスと証明書の関連付け][]」を参照してください。

2.  サービス定義ファイルで、**Endpoints** セクション内に **InputEndpoint** 要素を追加し、HTTPS を有効にします。

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                    certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

3.  サービス定義ファイルで、**Sites** セクション内に **Binding** 要素を追加します。これにより、HTTPS バインドが追加され、 エンドポイントがサイトにマップされます。

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Sites>
                <Site name="Web">
                    <Bindings>
                        <Binding name="HttpsIn" endpointName="HttpsIn" />
                    </Bindings>
                </Site>
            </Sites>
        ...
        </WebRole>

    サービス定義ファイルに対して必要な変更はすべて完了しましたが、サービス構成ファイルに証明書の情報を追加する必要もあります。

4.  サービス構成ファイル (CSCFG) である ServiceConfiguration.Cloud.cscfg で、**Role** セクション内に **Certificates** セクションを追加し、次に示すサムプリント値のサンプルを証明書のサムプリント値に置き換えます。

        <Role name="Deployment">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>

(上記の例では、サムプリント アルゴリズムに **sha1** を使用しています。証明書のサムプリント アルゴリズムに適切な値を指定してください。)

サービス定義ファイルとサービス構成ファイルが更新されたので、Azure にアップロードするためにデプロイをパッケージ化します。**cspack** を使用している場合は、**/generateConfigurationFile** フラグを使用しないようにしてください。このフラグによって、先ほど挿入した証明書情報が上書きされるためです。

## ステップ 3: 展開パッケージと証明書をアップロードする

デプロイ パッケージがこの証明書を使用するように更新され、HTTPS エンドポイントが追加されました。これで、管理ポータルを使用して Azure にパッケージと証明書をアップロードできるようになりました。

1. [Azure の管理ポータル][]にログインします。 
2. **[新規]**、**[クラウド サービス]**、**[カスタム作成]** の順にクリックします。
3. **[クラウド サービスを作成する]** ダイアログで、URL、リージョン/アフィニティ グループ、およびサブスクリプションの値を入力します。**[今すぐクラウド サービス パッケージを展開します]** チェック ボックスがオンになっていることを確認し、**[次へ]** をクリックします。
3. **[クラウド サービスの発行]** ダイアログで、クラウド サービスに必要な情報を入力し、環境で **[運用]** をクリックして、**[今すぐ証明書を追加]** チェック ボックスがオンになっていることを確認します(いずれかのロールに単一のインスタンスが含まれている場合は、**[1 つ以上のロールに単一のインスタンスが含まれている場合でも展開する]** チェック ボックスがオンになっていることを確認してください)。 

    ![クラウド サービスの発行][0]

4.  **[次へ]** をクリックします。
5.  **[証明書の追加]** ダイアログで、SSL 証明書 .pfx ファイルの場所と証明書のパスワードを入力し、**[証明書のアタッチ]** をクリックします。  

    ![証明書の追加][1]

6.  証明書が **[アタッチされた証明書]** セクションに表示されていることを確認します。

    ![アタッチされた証明書][4]

7.  **[完了]** をクリックしてクラウド サービスを作成します。展開の状態が **[準備完了]** になったら、次の手順に進むことができます。

## ステップ 4: HTTPS を使用してロール インスタンスに接続する

Azure でデプロイを実行できるようになったため、HTTPS を使用して接続できます。

1.  管理ポータルで展開を選択し、**[サイトの URL]** の下にあるリンクをクリックします。

    ![サイトの URL の確認][2]

2.  Web ブラウザーで、**http** ではなく **https** を使用するようにリンクを修正し、ページにアクセスします。

    **注:** 自己署名証明書を使用している場合は、自己署名証明書に関連付けられている HTTPS エンドポイントを参照すると、ブラウザーで証明書エラーが表示されます。信頼された証明機関によって署名された証明書を使用すると、この問題は解消されます。それまでの間、このエラーは無視してかまいません(また、信頼された証明書機関のユーザーの証明書ストアに自己署名証明書を追加する方法もあります)。

    ![SSL のサンプル Web サイト][3]

運用環境の展開ではなくステージング環境の展開に SSL を使用する場合は、最初に、ステージング環境の展開に使用されている URL を確認する必要があります。証明書または証明書情報を含めずに、ステージング環境にクラウド サービスを展開してください。展開すると、管理ポータルの **[サイトの URL]** に表示される、GUID ベースの URL を確認できます。GUID ベースの URL (**32818777-6e77-4ced-a8fc-57609d404462.cloudapp.net** など) と同じ共通名 (CN) で証明書を作成し、その証明書をステージングされたクラウド サービスに管理ポータルを使用して追加します。CSDEF ファイルと CSCFG ファイルに証明書情報を追加し、アプリケーションの再パッケージ化を実行して、新しいパッケージと CSCFG ファイルを使用するようステージング デプロイを更新します。

## その他のリソース

* [証明書をサービスに関連付ける方法][]

* [HTTPS エンドポイントでの SSL 証明書の構成方法][]

[Windows Azure のサービス証明書を作成する]: http://msdn.microsoft.com/library/azure/gg432987.aspx
[サービスと証明書の関連付け]: http://msdn.microsoft.com/library/azure/gg465718.aspx
[証明書をサービスに関連付ける方法]: http://msdn.microsoft.com/library/azure/gg465718.aspx
[Azure の管理ポータル]: http://manage.windowsazure.com
[0]: ./media/cloud-services-dotnet-configure-ssl-certificate/CreateCloudService.png
[1]: ./media/cloud-services-dotnet-configure-ssl-certificate/AddCertificate.png
[2]: ./media/cloud-services-dotnet-configure-ssl-certificate/CopyURL.png
[3]: ./media/cloud-services-dotnet-configure-ssl-certificate/SSLCloudService.png
[4]: ./media/cloud-services-dotnet-configure-ssl-certificate/AddCertificateComplete.png
[HTTPS エンドポイントでの SSL 証明書の構成方法]: http://msdn.microsoft.com/library/azure/ff795779.aspx

<!--HONumber=54-->
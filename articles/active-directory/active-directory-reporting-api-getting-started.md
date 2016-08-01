<properties
   pageTitle="Azure AD Reporting API の概要 | Microsoft Azure"
   description="Azure Active Directory Reporting API の概要について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/18/2016"
   ms.author="dhanyahk"/>

# Azure Active Directory Reporting API の概要

*このドキュメントは、[Azure Active Directory レポート ガイド](active-directory-reporting-guide.md)の一部です*。

Azure Active Directory (AD) には、アクティビティ、セキュリティ、監査に関するさまざまなレポートがあります。このデータは、Azure クラシック ポータルで使用できますが、SIEM システム、監査、ビジネス インテリジェンス ツールなど、他の多くのアプリケーションでも非常に役に立ちます。

[Azure AD Reporting API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) を使用すると、さまざまなプログラミング言語とツールから呼び出すことができる REST ベースの API セットにより、これらのデータにプログラムからアクセスできます。

この記事では、PowerShell を使用して Azure AD Reporting API を呼び出す手順について説明します。必要に応じて、サンプルの PowerShell スクリプトを変更し、使用可能な任意のレポートから、JSON、XML、またはテキストの形式でデータにアクセスできます。

このサンプルを使用するには、[Azure Active Directory](active-directory-whatis.md) テナントが必要です。

## API にアクセスする Azure AD アプリケーションの作成

Reporting API は、[OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) を使用して Web API へのアクセスを承認します。ディレクトリから情報にアクセスするには、Azure AD テナントでアプリケーションを作成し、Azure AD データにアクセスするための適切なアクセス許可を付与する必要があります。


### Azure AD アプリケーションの登録
Azure AD アプリケーションの登録作業を行うには、Azure AD テナントの全体管理者ディレクトリ ロールに属している Azure サブスクリプションの管理者アカウントで Azure クラシック ポータルにサインインする必要があります。Azure AD アプリケーションの登録に使用する権限には、全体管理者特権を持つアカウントでの登録/同意が必要であるためです。

> [AZURE.IMPORTANT] このような "admin" 特権を持った資格情報で実行されるアプリケーションはきわめて強力です。アプリケーションの ID/シークレットの資格情報は厳重に保管してください。


1. [Azure クラシック ポータル](https://manage.windowsazure.com/)に移動します。
2. 左側のウィンドウで **[Active Directory]** 拡張機能を選択し、Azure AD テナントに移動します。
3. **[アプリケーション]** タブに移動します。
4. 下のバーで **[追加]** をクリックします。
	- [組織で開発中のアプリケーションを追加] をクリックします。
	- **名前**: どのような名前でもかまいません。「Reporting API アプリケーション」のような名前をお勧めします。
	- **種類**: [Web アプリケーションや Web API] を選択します。
	- 矢印をクリックして次のページに進みます。
	- **サインオン URL**: ```https://localhost```。
	- **アプリケーション ID/URI**: ```https://localhost/ReportingApiApp```。
	- チェック マークをクリックして、アプリケーションの追加を完了します。

### API を使用するためのアクセス許可をアプリケーションに付与します。
1. **[アプリケーション]** タブに移動します。
2. 新しく作成したアプリケーションに移動します。
3. **[構成]** タブをクリックします。
4. [その他のアプリケーションに対するアクセス許可] セクションで、次の手順に従って設定します。
	- "Windows Azure Active Directory" リソース (Azure AD Graph API に対する権限) について、[アプリケーションのアクセス許可] ボックスの一覧の **[ディレクトリ データの読み取り]** を選択します。

        > [AZURE.IMPORTANT] ここには、必ず適切な権限を指定してください。適用するのは "アプリケーションのアクセス許可" であって "委任されたアクセス許可" ではありません。それ以外の場合、Reporting API にアクセスするために必要な権限レベルがアプリケーションに適用されません。スクリプトで *"Unable to check Directory Read access for appId (appId のディレクトリの読み取りアクセス権を確認できません)"* というエラーが発生します。


5. 下部のバーにある **[保存]** をクリックします。

### ディレクトリ ID、クライアント ID、クライアント シークレットの取得

以下の手順では、アプリケーションのクライアント ID とクライアント シークレットを取得する方法を説明します。また、テナントの名前を知る必要があります。これは、*.onmicrosoft.com またはカスタム ドメイン名です。これらの値を別の場所にコピーします。後でスクリプトを変更するときに使用します。

#### Azure AD テナントのドメイン名の取得
1. 左側のウィンドウで **[Active Directory]** 拡張機能を選択し、Azure AD テナントに移動します。
2. **[ドメイン]** タブに移動します。
4. 該当するテナントの "<tenant-name>.onmicrosoft.com" ドメイン名がカスタム ドメイン名 (構成されている場合) と共に表示されます。

#### アプリケーションのクライアント ID の取得
1. **[アプリケーション]** タブに移動します。
2. 新しく作成したアプリケーションに移動します。
3. **[構成]** タブに移動します。
4. アプリケーションのクライアント ID が、**[クライアント ID]** フィールドに一覧表示されます。

#### アプリケーションのクライアント シークレットの取得
1. **[アプリケーション]** タブに移動します。
2. 新しく作成したアプリケーションに移動します。
3. **[構成]** タブに移動します。
4. [キー] セクションで期間を選択して、アプリケーションの新しいシークレット キーを生成します。
5. このキーは保存時に表示されます。後で取得する方法がないため、忘れずにそれをコピーして安全な場所に貼り付けておいてください。

## スクリプトの変更
次のスクリプトのいずれかで、$ClientID、$ClientSecret、$tenantdomain を前出のセクションの説明に従って正しい値に置き換えることで、ディレクトリで動作するように編集します。

### PowerShell スクリプト
    # This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

    # Constants
    $ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
    $ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
    $loginURL       = "https://login.microsoftonline.com"     # AAD Instance; for example https://login.microsoftonline.com
    $tenantdomain   = "your-tenant-domain.onmicrosoft.com"    # AAD Tenant; for example, contoso.onmicrosoft.com
    $resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
    $7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
    Write-Output "Searching for events starting $7daysago"

    # Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    # Parse auditEvents report items, save output to file(s): auditEventsX.json, where X = 0 thru n for number of nextLink pages
    if ($oauth.access_token -ne $null) {   
        $i=0
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
        $url = 'https://graph.windows.net/' + $tenantdomain + '/reports/auditEvents?api-version=beta&`$filter=eventTime gt ' + $7daysago

        # loop through each query page (1 through n)
        Do{
            # display each event on the console window
            Write-Output "Fetching data using Uri: $url"
            $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
            foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
                Write-Output ($event | ConvertTo-Json)
            }
        
            # save the query page to an output file
            Write-Output "Save the output to a file auditEvents$i.json"
            $myReport.Content | Out-File -FilePath auditEvents$i.json -Force
            $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
            $i = $i+1
        } while($url -ne $null)
    } else {
        Write-Host "ERROR: No Access Token"
        }

    Write-Host "Press any key to continue ..."
    $x = $host.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown")

### Bash スクリプト

    #!/bin/bash

    # Author: Ken Hoff (kenhoff@microsoft.com)
    # Date: 2015.08.20
    # NOTE: This script requires jq (https://stedolan.github.io/jq/)

    CLIENT_ID="your-application-client-id-here"         # Should be a ~35 character string insert your info here
    CLIENT_SECRET="your-application-client-secret-here" # Should be a ~44 character string insert your info here
    LOGIN_URL="https://login.windows.net"
    TENANT_DOMAIN="your-directory-name-here.onmicrosoft.com"    # For example, contoso.onmicrosoft.com

    TOKEN_INFO=$(curl -s --data-urlencode "grant_type=client_credentials" --data-urlencode "client_id=$CLIENT_ID" --data-urlencode "client_secret=$CLIENT_SECRET" "$LOGIN_URL/$TENANT_DOMAIN/oauth2/token?api-version=1.0")

    TOKEN_TYPE=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.token_type')
    ACCESS_TOKEN=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.access_token')

    # get yesterday's date

    YESTERDAY=$(date --date='1 day ago' +'%Y-%m-%d')

    URL="https://graph.windows.net/$TENANT_DOMAIN/reports/auditEvents?api-version=beta&$filter=eventTime%20gt%20$YESTERDAY"


    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" $URL)

    echo $REPORT | ./jq-win64.exe -r '.value' | ./jq-win64.exe -r ".[]"

### Python
	# Author: Michael McLaughlin (michmcla@microsoft.com)
	# Date: January 20, 2016
	# This requires the Python Requests module: http://docs.python-requests.org

	import requests
	import datetime
	import sys

	client_id = 'your-application-client-id-here'
	client_secret = 'your-application-client-secret-here'
	login_url = 'https://login.windows.net/'
	tenant_domain = 'your-directory-name-here.onmicrosoft.com'

	# Get an OAuth access token
	bodyvals = {'client_id': client_id,
	            'client_secret': client_secret,
	            'grant_type': 'client_credentials'}

	request_url = login_url + tenant_domain + '/oauth2/token?api-version=1.0'
	token_response = requests.post(request_url, data=bodyvals)

	access_token = token_response.json().get('access_token')
	token_type = token_response.json().get('token_type')

	if access_token is None or token_type is None:
	    print "ERROR: Couldn't get access token"
	    sys.exit(1)

	# Use the access token to make the API request
	yesterday = datetime.date.strftime(datetime.date.today() - datetime.timedelta(days=1), '%Y-%m-%d')

	header_params = {'Authorization': token_type + ' ' + access_token}
	request_string = 'https://graph.windows.net/' + tenant_domain + '/reports/auditEvents?api-version=beta&$filter=eventTime%20gt%20' + yesterday   
	response = requests.get(request_string, headers = header_params)

	if response.status_code is 200:
	    print response.content
	else:
	    print 'ERROR: API request failed'


## スクリプトの実行
スクリプトの編集が完了したら、実行して、AuditEvents レポートから予期したデータが返されることを確認します。

スクリプトからは、auditEvents レポートの出力が JSON 形式で返されます。また、同じ出力内容を使って `auditEvents.json` ファイルも作成されます。他のレポートからデータを返すようにスクリプトを変更してテストしたり、必要のない出力形式をコメント化したりできます。

## メモ

- Azure AD Reporting API によって (OData 改ページ調整を使用) 返されるイベントの数に制限はありません。　
- レポート データの保持制限については、[レポートの保持ポリシー](active-directory-reporting-retention.md)を参照してください。


## 次のステップ
- 使用可能なセキュリティ、監査、およびアクティビティ レポートに興味がある場合は、 [Azure AD のセキュリティ、監査、アクティビティ レポート](active-directory-view-access-usage-reports.md)に関するページを参照してください。提供されているすべての Azure AD Graph API エンドポイントは、[https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta](https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta) にアクセスして確認することもできます。この点については、[Azure AD のレポートとイベント (プレビュー)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview) に関する記事の中で触れられています。
- 監査レポートの詳細については、「[Azure Active Directory 監査レポートのイベント](active-directory-reporting-audit-events.md)」を参照してください
- Azure AD Graph API REST サービスの詳細については、「[Azure AD Reports and Events (Preview) (Azure AD のレポートとイベント (プレビュー))](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview)」を参照してください

<!---HONumber=AcomDC_0720_2016-->
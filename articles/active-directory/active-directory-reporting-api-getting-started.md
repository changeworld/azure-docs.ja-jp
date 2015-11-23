<properties
   pageTitle="Azure AD Reporting API の概要"
   description="Azure Active Directory Reporting API の概要について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="kenhoff"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="11/09/2015"
   ms.author="kenhoff"/>


# Azure AD Reporting API の概要

Azure Active Directory には、アクティビティ、セキュリティ、監査に関するさまざまなレポートがあります。このデータは、Azure ポータルで使用できますが、SIEM システム、監査、ビジネス インテリジェンス ツールなど、他の多くのアプリケーションでも非常に役に立ちます。

Azure AD Reporting API を使用すると、さまざまなプログラミング言語とツールから呼び出すことができる REST ベースの API セットにより、これらのデータにプログラムからアクセスできます。

この記事では、PowerShell を使用して Azure AD Reporting API を呼び出す手順について説明します。必要に応じて、サンプルの PowerShell スクリプトを変更し、使用可能な任意のレポートから、JSON、XML、またはテキストの形式でデータにアクセスできます。

このサンプルを使用するには、[Azure Active Directory](active-directory-whatis.md) が必要です。

## API にアクセスする Azure AD アプリケーションの作成

Reporting API は、[OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) を使用して Web API へのアクセスを承認します。ディレクトリから情報にアクセスするには、Active Directory でアプリケーションを作成し、AAD データにアクセスするための適切なアクセス許可を付与する必要があります。


### アプリケーションの作成
- [Microsoft Azure 管理ポータル](https://manage.windowsazure.com/)に移動します。
- ディレクトリに移動します。
- アプリケーションに移動します。
- 下のバーで [追加] をクリックします。
	- [組織で開発中のアプリケーションを追加] をクリックします。
	- **名前**: どのような名前でもかまいません。「Reporting API アプリケーション」のような名前をお勧めします。
	- **種類**: [Web アプリケーションや Web API] を選択します。
	- 矢印をクリックして次のページに進みます。
	- **サインオン URL**: ```http://localhost```。
	- **アプリケーション ID/URI**: ```http://localhost```。
	- チェック マークをクリックして、アプリケーションの追加を完了します。

### API を使用するためのアクセス許可をアプリケーションに付与します。
- [アプリケーション] タブに移動します。
- 新しく作成したアプリケーションに移動します。
- **[構成]** タブをクリックします。
- [その他のアプリケーションに対するアクセス許可] セクションで、次の手順に従って設定します。
	- [Microsoft Azure Active Directory] の [アプリケーションのアクセス許可] で、**[ディレクトリ データの読み取り]** をオンにします。
- 下部のバーにある **[保存]** をクリックします。


### ディレクトリ ID、クライアント ID、クライアント シークレットの取得

以下の手順では、アプリケーションのクライアント ID とクライアント シークレットを取得する方法を説明します。また、テナントの名前を知る必要があります。これは、*.onmicrosoft.com またはカスタム ドメイン名です。これらを別の場所にコピーします。スクリプトを変更するときに使用します。

#### アプリケーション クライアント ID
- [アプリケーション] タブに移動します。
- 新しく作成したアプリケーションに移動します。
- **[構成]** タブに移動します。
- アプリケーションのクライアント ID が、**[クライアント ID]** フィールドに一覧表示されます。

#### アプリケーション クライアント シークレット
- [アプリケーション] タブに移動します。
- 新しく作成したアプリケーションに移動します。
- [構成] タブに移動します。
- [キー] セクションで期間を選択して、アプリケーションの新しいシークレット キーを生成します。
- このキーは保存時に表示されます。後で取得する方法がないため、忘れずにそれをコピーして安全な場所に貼り付けておいてください。


## スクリプトの変更
次のスクリプトのいずれかで、$ClientID、$ClientSecret、$tenantdomain を「Delegating Access in Azure AD (Azure AD でのアクセスの委任)」の説明に従って正しい値に置き換えることで、ディレクトリで動作するように編集します。

### PowerShell スクリプト

    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID      = "<<YOUR CLIENT ID HERE>>"                # Should be a ~35 character string insert your info here
    $ClientSecret  = "<<YOUR CLIENT SECRET HERE>>"          # Should be a ~44 character string insert your info here
    $loginURL      = "https://login.windows.net"
    $tenantdomain  = "<<YOUR TENANT NAME HERE>>"            # For example, contoso.onmicrosoft.com

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    if ($oauth.access_token -ne $null) {
        $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        # Returns a list of all the available reports
        Write-host List of available reports
        Write-host =========================
        $allReports = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports?api-version=beta")
        Write-host $allReports.Content

        Write-host
        Write-host Data from the AccountProvisioningEvents report
        Write-host ====================================================
        Write-host
        # Returns a JSON document for the "accountProvisioningEvents" report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/accountProvisioningEvents?api-version=beta")
        Write-host $myReport.Content

        Write-host
        Write-host Data from the AuditEvents report with datetime filter
        Write-host ====================================================
        Write-host
        # Returns a JSON document for the "auditEvents" report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/auditEvents?api-version=beta&$filter=eventTime gt 2015-05-20")
        Write-host $myReport.Content

        # Options for other output formats

        # to output the JSON use following line
        $myReport.Content | Out-File -FilePath accountProvisioningEvents.json -Force

        # to output the content to a name value list
        ($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath accountProvisioningEvents.txt -Force

        # to output the content in XML use the following line
        (($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath accountProvisioningEvents.xml -Force

    } else {
        Write-Host "ERROR: No Access Token"
        }

### Bash スクリプト

    #!/bin/bash

    # Author: Ken Hoff (kenhoff@microsoft.com)
    # Date: 2015.08.20
    # NOTE: This script requires jq (https://stedolan.github.io/jq/)

    CLIENT_ID="<<YOUR CLIENT ID HERE>>"			# Should be a ~35 character string insert your info here
    CLIENT_SECRET="<<YOUR CLIENT SECRET HERE>>"	# Should be a ~44 character string insert your info here
    LOGIN_URL="https://login.windows.net"
    TENANT_DOMAIN="<<YOUR TENANT NAME HERE>>"	 # For example, contoso.onmicrosoft.com

    TOKEN_INFO=$(curl -s --data-urlencode "grant_type=client_credentials" --data-urlencode "client_id=$CLIENT_ID" --data-urlencode "client_secret=$CLIENT_SECRET" "$LOGIN_URL/$TENANT_DOMAIN/oauth2/token?api-version=1.0")

    TOKEN_TYPE=$(echo $TOKEN_INFO | jq -r '.token_type')
    ACCESS_TOKEN=$(echo $TOKEN_INFO | jq -r '.access_token')

    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" https://graph.windows.net/$TENANT_DOMAIN/reports/auditEvents?api-version=beta)

    echo $REPORT | jq -r '.value' | jq -r ".[]"




## スクリプトの実行
スクリプトの編集が完了したら、実行して、AuditEvents レポートから予期したデータが返されることを確認します。

スクリプトは、使用可能なすべてのレポートの一覧、および AccountProvisioningEvents レポートからの出力を、PowerShell ウィンドウに JSON 形式で返します。また、同じ出力を JSON、テキスト、XML でファイルに作成します。他のレポートからデータを返すようにスクリプトを変更してテストしたり、必要のない出力形式をコメント化したりできます。

## メモ

- Azure AD Reporting API によって (OData 改ページ調整を使用) 返されるイベントの数に制限はありません。　
	- レポート データの保持制限については、[レポートの保持ポリシー](active-directory-reporting-retention.md)を参照してください。


## 次のステップ
- 使用可能なセキュリティ、監査、およびアクティビティ レポートに興味がある場合は、 「[View your access and usage reports (アクセスおよび使用状況のレポートの表示)](active-directory-view-access-usage-reports.md)」を参照してください
- 監査レポートの詳細については、「[Azure Active Directory 監査レポートのイベント](active-directory-reporting-audit-events.md)」を参照してください
- Graph API REST サービスの詳細については、「[Azure AD Reports and Events (Preview) (Azure AD のレポートとイベント (プレビュー))](https://msdn.microsoft.com/library/azure/mt126081.aspx)」を参照してください

<!---HONumber=Nov15_HO3-->
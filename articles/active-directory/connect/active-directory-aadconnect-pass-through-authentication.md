---
title: "Azure AD Connect: パススルー認証 | Microsoft Docs"
description: "このトピックでは、Azure AD パススルー認証をオンプレミス Active Directory (AD) で使用して、Azure Active Directory (Azure AD) と接続済みサービスへのアクセス権限を提供する方法を説明します。"
services: active-directory
keywords: "Azure AD Connect とは, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン"
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 28b5da6098316f8fbe84966e0dac88f5b7d2cb1d
ms.openlocfilehash: 88d315c68c545b84833582ca6aeeed617bcbb75e


---

# <a name="what-is-azure-ad-pass-through-authentication"></a>Azure AD パススルー認証とは
会社のリソースとクラウド ベースのサービスへのアクセスに同じ資格情報 (ユーザー名とパスワード) を使うことができれば、ユーザーは異なる資格情報を覚えておく必要がなくなり、ログイン方法を忘れてしまうといった問題も少なくなります。  さらに、パスワードのリセットをヘルプ デスクに依頼するといった手間も省けるというメリットもあります。  

Azure AD のパスワード同期機能を利用して1つの資格情報だけでオンプレミスやクラウドのサービスにアクセスできるようにしている組織が増える一方で、ユーザーの利便性はお構いなしに、各部署が担当するサービスごとにそれぞれのパスワードの入力が求められる組織もあります。  

Azure AD パススルー認証は、オンプレミス Active Directory に対して Azure AD サービスのパスワードの認証を確実に行うシンプルなソリューションを提供しており、複雑なネットワーク インフラストラクチャを導入したり、クラウドに何らかの形式でオンプレミス パスワードを配置する必要がありません。  

シングル サインオンと組み合わせると、ユーザーが Azure AD や他のクラウド サービスにログインする際にパスワードを入力する必要もなくなり、真に統合されたサービスを企業内のコンピューターで利用できるようになります。

![パススルー認証](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

パススルー認証は AAD Connect を通じて構成でき、パスワードの検証要求をリッスンするシンプルなオンプレミス エージェントを使用します。  エージェントは複数のコンピューターに簡単にデプロイでき、高可用性と負荷分散を実現できます。  すべての通信は送信のみであるため、DMZ を使用したり、DMZ にコネクタをインストールする必要がありません。  コネクタを使用する際のコンピューターの要件は次のとおりです。


- Windows Server 2012 以降 
- ユーザーの検証が行われるフォレスト内のドメインに参加している

## <a name="supported-clients-in-the-preview"></a>プレビューでサポートされているクライアント
パススルー認証は、Web ブラウザー ベースのクライアントと、最新の認証をサポートする Office クライアントでサポートされています。  従来の Office クライアント、Exchange Active Sync (モバイル デバイスのネイティブ メール クライアントなど) のように、サポートされていないクライアントの場合、同等の最新の認証手段を使用することをお勧めします。  そうすることで、パススルー認証が利用できるだけでなく、多要素認証などの条件付きアクセスを適用することもできます。

現在のところ、Azure AD に参加している Windows 10 を使用している場合は、パススルー認証がサポートされません。  ただし、Windows 10 では上で述べた従来のクライアント以外にも、パスワード ハッシュ同期を自動フォールバックとして使用できます。
>[!NOTE] 
>プレビューでは、Azure AD Connect のサインイン オプションとしてパススルー認証を選択した場合、パスワード ハッシュ同期が既定で有効になります。 これは、Azure AD Connect のオプション画面で無効にできます。

## <a name="how-azure-ad-pass-through-authentication-works"></a>Azure AD パススルー認証のしくみ
ユーザーが Azure AD ログイン ページでユーザー名とパスワードを入力すると、Azure AD はユーザー名とパスワードを検証用のオンプレミス コネクタのキューに配置します。  その後、利用可能ないずれかのオンプレミス コネクタがユーザー名とパスワードを取得し、Active Directory に対して検証します。  検証は標準の Windows API を通じて行われます。これは、Active Directory フェデレーション サービスがパスワードを検証するのと同様の方法です。

次に、オンプレミス ドメイン コントローラーが要求を評価し、コネクタに応答を返します。応答はその後コネクタから Azure AD に返されます。 そうすると、Azure AD が応答を評価し、必要に応じてトークンを発行したり、多要素認証を要求したりしてユーザーに応答します。  次の図は、さまざまな手順を示しています。


![パススルー認証](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="azure-ad-pass-through-prerequisites"></a>Azure AD パススルーの前提条件
Azure AD のパススルー認証を有効にして使用するには、次の条件を満たす必要があります。 


- Azure AD Connect ソフトウェア
- 自分がグローバル管理者となっている Azure AD ディレクトリ  

>[!NOTE] 
>オンプレミス サービスが失敗したり利用できなくなった際にテナントの構成を管理できるよう、アカウントはクラウド専用の管理者アカウントにすることをお勧めします。


- AAD Connect ツールを実行するための Windows Server 2012 R2 以降のサーバー。  このコンピューターは、検証対象のユーザーと同じフォレストのメンバーである必要があります。
- Azure AD と同期するユーザーを含むフォレストが複数ある場合は、フォレスト間で信頼関係を設定する必要があります。 
- 高可用性と負荷分散のための 2 つ目のコネクタを実行するための、Windows Server 2012 R2 を実行する 2 つ目のサーバー。  このコネクタのデプロイ手順については後述します。
- コネクタと Azure AD の間にファイアウォールがある場合は、次の点をご確認ください。
    - URL フィルタリングが有効になっている場合は、コネクタが次の URL と通信できることをご確認ください。 
        -  *.msappproxy.net
        -  *.servicebus.windows.net  
        -  また、コネクタは Azure データ センターの IP 範囲に対して直接 IP 接続で接続します。 
    - コネクタがクライアント証明書を使用して Azure AD と通信する際に、ファイアウォールによって SSL 検査が実行されないことを確認します。
    - コネクタが、次のポートで Azure AD に HTTPS (TCP) 要求を実行できることを確認します。 

|ポート番号|説明
| --- | ---
|80|SSL などのセキュリティ検証用の送信 HTTP トラフィックに使用されます。
|443|   Azure AD に対するユーザー認証に使用されます。
|8080/443|  コネクタのブートストラップ シーケンスのほか、コネクタの自動更新に使用されます。
|9090|  コネクタの登録に使用されます (コネクタ登録プロセスでのみ必要)。
|9091|  コネクタの信頼証明書の自動更新に使用されます。
|9352、5671|    コネクタと Azure サービスの間で受信要求の通信に使用されます。
|9350|  [省略可能] 受信要求のパフォーマンス向上に使用されます。
|10100–10120|   コネクタから Azure AD への応答に使用されます。

ご利用のファイアウォールが送信元ユーザーに応じてトラフィックを監視している場合は、Network Service として実行されている Windows サービスを送信元とするトラフィックに対してこれらのポートを開放します。 また、NT Authority\System に対しては必ずポート 8080 を有効にしてください。

## <a name="enabling-pass-through-authentication"></a>パススルー認証の有効化
Azure AD パススルー認証は Azure AD Connect を通じて有効にできます。  パススルー認証を有効にすると、Azure AD Connect と同じサーバーに 1 つ目のコネクタがデプロイされます。  Azure AD Connect をインストールするときには、カスタム インストールを選択し、サインインのオプション ページでパススルー認証を選択します。 詳細については、「[Azure AD Connect のカスタム インストール](active-directory-aadconnect-get-started-custom.md)」を参照してください。

![シングル サインオン](./media/active-directory-aadconnect-sso/sso3.png)

既定では、1 つ目のパススルー認証コネクタは Azure AD Connect サーバーにインストールされます。  認証要求の高可用性と負荷分散を利用する場合は、別のコンピューターに 2 つ目のコネクタをデプロイする必要があります。

2 つ目のコネクタをデプロイするには、次の手順を実行します。

### <a name="step-1-install-the-connector-without-registration"></a>手順 1: 登録せずにコネクタをインストールする

1.  最新の[コネクタ](https://go.microsoft.com/fwlink/?linkid=837580)をダウンロードします。
2.  管理者としてコマンド プロンプトを開きます。
3.  次のコマンドを実行します。/q はサイレント インストールを意味します。つまり、インストールで使用許諾契約書への同意を求めるメッセージは表示されません。

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

### <a name="step-2-register-the-connector-with-azure-ad-for-pass-through-authentication"></a>手順 2: パススルー認証用に Azure AD にコネクタを登録します。

1.  管理者として PowerShell ウィンドウを開きます。
2.  “C:\Program Files\Microsoft AAD App Proxy Connector” に移動して次のスクリプトを実行します。
.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication
3.  メッセージが表示されたら、Azure AD テナント管理者アカウントの資格情報を入力します。

## <a name="troubleshooting-pass-through-authentication"></a>パススルー認証のトラブルシューティング
パススルー認証のトラブルシューティングにおいては、発生する問題をいくつかのカテゴリに分けることができます。  問題の種類に応じて、確認する場所が異なる場合があります。

コネクタに関するエラーであれば、コネクタ コンピューターの “Application and Service Logs\Windows\AadApplicationProxy\Connector\Admin” のイベント ログを確認します。  必要に応じて、分析とデバッグ ログを有効にし、コネクタのセッション ログを有効にすることで、より詳細なログを使用できます。  このログを既定で有効にして実行することはお勧めしません。

C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace にあるコネクタのトレース ログでも詳細を確認できます。 これらのログにも、個々のユーザーのパススルー認証の失敗の原因が記録されます。たとえば、次のエントリにはエラー コード 1328 が記録されています。

    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ

コマンド プロンプトを起動して次のコマンドを実行すると、エラーの詳細を確認できます。 ('1328' は要求のエラー番号に置き換えてください)。

Net helpmsg 1328

結果は次のようになります。

![パススルー認証](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

また、監査ログが有効になっている場合、ドメイン コントローラーのセキュリティ ログでも詳細を確認できます。  コネクタによる認証要求に対しては次のようにクエリを実行します。

    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>

その他に、Azure AD ログイン画面に次のようなエラーが、その解決手順とともに表示されます。

|エラー|Description|解決策
| --- | --- | ---
|AADSTS80001|Unable to connect to Active Directory (Active Directory に接続できません)|Ensure that the connector machines are domain joined and are able to connect to Active Directory. (コネクタ コンピューターがドメインに参加しており、Active Directory に接続できることを確認してください)  
|AADSTS8002|A timeout occurred connecting to Active Directory (Active Directory への接続中にタイムアウトが発生しました)|Check to ensure that Active Directory is available and responding to requests from the connector. (Active Directory が使用可能で、コネクタからの要求に応答していることを確認してください)
|AADSTS80004|The username passed to the connector was not valid (コネクタに渡されたユーザー名が無効です)|Ensure the user is attempting to login with the right username. (ログインを試みているユーザーのユーザー名に間違いがないことを確認してください)
|AADSTS80007|An error occurred communicating with Active Directory (Active Directory との通信中にエラーが発生しました)|Check the connector logs for more information and verify that Active Directory is operating as expected. (コネクタ ログで詳細を確認し、Active Directory が期待通りに動作していることを確認してください)








<!--HONumber=Dec16_HO3-->



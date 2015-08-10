<properties 
	pageTitle="クラウド内のオンプレミスの ID インフラストラクチャの監視" 
	description="これは、Azure AD Connect Health の内容と使用する理由を説明するページです。" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2015" 
	ms.author="billmath"/>

# クラウド内のオンプレミスの ID インフラストラクチャの監視

Azure AD Connect Health では、オンプレミスの ID インフラストラクチャを監視し、分析することができます。アラート、パフォーマンス、使用パターン、構成設定を表示する機能があり、Office 365 に対して信頼性の高い接続を維持するなどさまざまなことができます。これらは、対象のサーバーにインストールされているエージェントを使用して実現されます。Azure AD Connect Health の要件とインストールの詳細については、「[Azure AD Connect Health の要件](active-directory-aadconnect-health-requirements.md)」を参照してください。

![What is Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnecthealth2.png)


この情報は、すべて Azure AD Connect Health ポータルで参照できます。Azure AD Connect Health ポータルを使用すると、アラート、パフォーマンスの監視、および使用状況の分析を表示できます。この情報はすべて 1 つの使いやすい場所に表示されるので、必要な情報を探して時間を無駄にすることはありません。

![What is Azure AD Connect Health](./media/active-directory-aadconnect-health/usage.png)

Azure AD Connect Health の今後の更新には、監視の追加と、他の ID コンポーネントやサービス (Azure AD Connect の同期サービスなど) の分析が含まれる予定です。このように ID というレンズを通した単一のダッシュボードが提供されることで、より堅牢で健全な統合環境を設定できるようになります。ユーザーはこの環境を利用して作業能力を向上させることができます。


![What is Azure AD Connect Health](./media/active-directory-aadconnect-health/logo1.png)




## Azure AD Connect Health を使用する理由

オンプレミスのディレクトリと Azure AD を統合すると、クラウドとオンプレミス両方のリソースに共通の ID が提供されるため、ユーザーの生産性が向上します。ただし、この統合には課題があります。ユーザーが任意のデバイスからオンプレミスとクラウド両方のリソースに安全にアクセスできるように、環境を健全に保つ必要があります。Azure AD Connect Health では、Office 365 や他の Azure AD アプリケーションにアクセスするときに使用するオンプレミスの ID インフラストラクチャを、簡単なクラウドベースのアプローチで監視し分析することができます。オンプレミスの各 ID サーバーにエージェントをインストールするぐらいにシンプルです。

Azure AD Connect Health for AD FS は、Windows Server 2008/2008 R2 の AD FS 2.0 と Windows Server 2012/2012R2 の AD FS をサポートします。これらには、エクストラネット アクセスの認証をサポートする AD FS プロキシまたは Web アプリケーション プロキシ サーバーも含まれます。Azure AD Connect Health for AD FS の主な機能セットは次のとおりです。

- Azure AD などの AD FS が保護するアプリケーションへの信頼性の高いアクセスを保証するためにアラートを表示し対処する
- 重大なアラートの電子メール通知を送信する
- 容量計画を決定するパフォーマンス データを表示する
- 異常を判定するため、または容量計画のベースラインを確立するために AD FS ログイン パターンを詳細に表示する

次のビデオでは、Azure AD Connect Health の概要について説明します。

[AZURE.VIDEO azure-ad-connect-health--monitor-you-identity-bridge]


## Azure ポータルから Azure Active Directory Connect Health を初めて使用する
Azure Active Directory Connect Health の使用を開始するには、次の手順に従います。Azure AD Connect Health のインスタンスでデータを表示するためには、あらかじめ対象サーバーに Azure AD Connect Health エージェントをインストールしておく必要があります。Azure AD Connect Health エージェントをダウンロードするには、最初のブレードで [クイック スタート]、[ツールの取得] の順に選択します。次のリンクを使用して、エージェントを直接ダウンロードすることもできます。Azure Active Directory Connect Health を使用するには、次の手順に従います。

1. [Microsoft Azure ポータル](https://portal.azure.com/)にサインインします。
2. Azure Active Directory Connect Health にアクセスするには、Marketplace に移動して検索するか、Marketplace を選択して [セキュリティ + ID] を選択します。
3. 概要ブレード (ブレードは、全体的なビューの 1 つの部分です。ウィンドウやフライアウトと考えることができます) で、[作成] をクリックします。ディレクトリの情報を含む別のブレードが開きます。
4. ディレクトリのブレードで、[作成] をクリックします。Azure Active Directory Premium ライセンスをお持ちでない場合、Azure AD Connect Health を使用するにはこのライセンスが必要になります。Azure AD Premium の詳細については、Azure AD Premium の概要に関するページを参照してください。






![Azure AD Connect Health Portal](./media/active-directory-aadconnect-health/portal1.png)



## Azure Active Directory Connect Health ポータル
Azure AD Connect Health ポータルを使用すると、アラート、パフォーマンスの監視、および使用状況の分析を表示できます。Azure AD Connect Health に初めてアクセスすると、最初のブレードに表示されます。ブレードは、全体的なビューの 1 つの部分です。ブレードは、ウィンドウと考えることができます。最初のブレードには、クイック スタート、サービス、構成が表示されます。スクリーンショットの下に、それぞれの簡単な説明を示します。

![Azure AD Connect Health Portal](./media/active-directory-aadconnect-health/portal2.png)

- **[クイック スタート]** – これを選択すると [クイック スタート] ブレードが開きます。ここで、[ツールの取得] を選択して Azure AD Connect Health エージェントをダウンロードしたり、ドキュメントにアクセスしたり、フィードバックを提供したりできます。
- **[Active Directory フェデレーション サービス]** – Azure AD Connect Health が現在監視しているすべての AD FS サービスを表します。このセクションに表示されるオプションについては、以下のセクションで説明します。「Azure Active Directory Connect Health サービス」を参照してください。
- [構成] – 以下の機能のオンとオフを切り替えることができます。
<ol>
1. Azure AD Connect Health エージェントを最新バージョンに自動更新する - Azure AD Connect Health エージェントの最新バージョンが使用可能になると、自動的に更新されます。この機能は、既定では有効になっています。
2. Microsoft がトラブルシューティングの目的でのみ Azure AD ディレクトリの正常性データにアクセスすることを許可する - この機能が有効になっていると、ユーザーに表示されているデータと同じデータを Microsoft が表示できます。これは問題のトラブルシューティングと支援に役立ちます。この機能は、既定では無効になっています。




## Azure Active Directory Connect Health サービス
このセクションには、Azure AD Connect Health が監視しているアクティブなサービスとそのサービスのインスタンスが表示されます。省略記号 ([...]) をクリックするとブレードが開き、すべてのインスタンスが表示されます。

![Azure AD Connect Health Services](./media/active-directory-aadconnect-health/portal3.png)

いずれかのインスタンスを選択すると、Azure AD Connect Health のブレードが開き、そのサービス インスタンスに関する情報が表示されます。ここでは、インスタンスに関するさまざまな情報を確認できます。この情報には、概要、プロパティ、アラート、監視、使用状況の分析などが含まれます。これらの詳細については、このページの上部にある以降のセクションのリンクを参照してください。



----------------------------------------------------------------------------------------------------------
## Azure AD Connect Health エージェントのダウンロード

Azure AD Connect Health の使用を開始するには、こちら ([Azure AD Connect Health エージェントのダウンロード](http://go.microsoft.com/fwlink/?LinkID=518973)) から最新のバージョンのエージェントをダウンロードします。 エージェントをインストールする前に、Marketplace からサービスを追加したことを確認します。

----------------------------------------------------------------------------------------------------------

## Azure Active Directory Connect Health のアラート
Azure AD Connect Health アラート セクションには、アクティブなアラートの一覧が表示されます。各アラートには、関連情報、解決の手順、関連ドキュメントのリンクが含まれます。アクティブまたは解決済みのアラートを選択すると、新しいブレードが開き、アラートの解決に利用できる手順やその他のドキュメントへのリンクなどの追加情報が表示されます。過去に解決されたアラートの履歴データも表示できます。

![Azure AD Connect Health Portal](./media/active-directory-aadconnect-health/alert1.png)

アラートを選択すると、アラートの解決に利用できる手順やその他のドキュメントへのリンクなどの追加情報が表示されます。

![Azure AD Connect Health Portal](./media/active-directory-aadconnect-health/alert2.png)

## Azure Active Directory Connect Health のパフォーマンスの監視
Azure AD Connect Health のパフォーマンスの監視は、メトリックに関する監視情報を提供します。[監視] ボックスを選択すると、ブレードが開き、メトリックに関する詳細情報が表示されます。


![Azure AD Connect Health Portal](./media/active-directory-aadconnect-health/perf1.png)


ブレードの上部にある [フィルター] を選択すると、サーバーごとにフィルター処理して個々のサーバーのメトリックを表示することができます。メトリックを変更するには、監視ブレードの監視グラフを右クリックし、[グラフの編集] を選択します。次に、開いた新しいブレードのドロップダウンから追加のメトリックを選択し、パフォーマンス データを表示する時間範囲を指定します。


![Azure AD Connect Health Portal](./media/active-directory-aadconnect-health/perf2.png)

## Azure Active Directory Connect Health の利用状況分析とレポート
Azure AD Connect Health 利用状況分析では、フェデレーション サーバーの認証トラフィックを分析できます。[利用状況分析] ボックスを選択すると、[利用状況分析] ブレードが開き、メトリックとグループ分けが表示されます。

>[AZURE.NOTE]AD FS で利用状況分析を使用するためには、AD FS 監査が有効になっている必要があります。詳細については、「Azure AD Connect Health の要件」を参照してください。

![Azure AD Connect Health Portal](./media/active-directory-aadconnect-health/report1.png)

追加のメトリックを選択するには、時間範囲を指定します。グループ分けを変更するには、利用状況の分析グラフを右クリックし、[グラフの編集] を選択します。次に時間範囲を指定し、メトリックを変更または選択して、グループ分けを変更します。さまざまな "メトリック" に基づいて認証トラフィックの分布を確認し、次に示す "グループ化" という関連パラメーターを使用して各メトリックをグループ化できます。

| メトリック | グループ化 | グループ化の意味と役立つ理由 |
| ------ | -------- | -------------------------------------------- |
| 合計要求数: フェデレーション サービスによって処理された要求の合計数 | すべて | グループ化されていない要求の合計数が表示されます。 |
| | アプリケーション | このオプションは、対象となる証明書利用者に基づいて、要求の合計をグループ化します。このグループ化は、どのアプリケーションがトラフィック全体のどの程度の割合を受信しているかを把握するのに役立ちます。 |
| | サーバー | このオプションは、要求を処理したサーバーに基づいて、要求の合計をグループ化します。このグループ化は、トラフィック全体の負荷分散を把握するのに役立ちます。 |
| | 社内参加 | このオプションは、社内参加している (既知の) デバイスから要求が行われたかどうかに基づいて、要求の合計をグループ化します。このグループ化は、ID インフラストラクチャに対して未知のデバイスを使用してリソースがアクセスされているかどうかを把握するのに役立ちます。 |
| | 認証方法 | このオプションは、認証に使用された認証方法に基づいて、要求の合計をグループ化します。このグループ化は、認証に使用される共通の認証方法を把握するのに役立ちます。考えられる認証方法を以下に示します。<ol> <li>Windows 統合認証 (Windows)</li> <li>フォーム ベース認証 (フォーム)</li> <li>SSO (シングル サインオン)</li> <li>X509 証明書認証 (証明書)</li> <br>フェデレーション サーバーで SSO の Cookie を含む要求を受信する場合、要求が SSO (シングル サインオン) としてカウントされることに注意してください。このような場合、Cookie が有効であれば、ユーザーは資格情報の提供を要求されずに、シームレスにアプリケーションにアクセスできます。これは、フェデレーション サーバーによって保護される証明書利用者が複数ある場合でも共通です。 |
| | ネットワークの場所 | このオプションは、ユーザーのネットワークの場所に基づいて、要求の合計をグループ化します。イントラネットまたはエクストラネットを指定できます。このグループ化は、イントラネットからのトラフィックとエクストラネットからのトラフィックの割合を把握するのに役立ちます。 |
| 失敗した要求の合計数: フェデレーション サービスによって処理され、失敗した要求の合計数。<br> (このメトリックは、Windows Server 2012 R2 の AD FS でのみ使用できます)| エラーの種類 | あらかじめ定義されたエラーの種類に基づいて、エラーの数を表示します。このグループ化は、一般的なエラーの種類を把握するのに役立ちます。<ul><li>"ユーザー名またはパスワードが正しくない": 正しくないユーザー名またはパスワードによるエラー。</li> <li>"エクストラネット ロックアウト": エクストラネットからロックアウトされたユーザーから受信した要求によるエラー。</li><li> "パスワードの有効期限が切れている": ユーザーが期限切れのパスワードを使用してログインしたことによるエラー。</li><li>"無効なアカウント": ユーザーが無効なアカウントを使用してログインしたことによるエラー。</li><li>"デバイス認証": ユーザーがデバイス認証を使用した認証に失敗したことによるエラー。</li><li>"ユーザー証明書の認証": ユーザーが無効な証明書が原因で認証に失敗したことによるエラー。</li><li>"MFA": ユーザーが Multi-Factor Authentication を使用した認証に失敗したことによるエラー。</li><li>"その他の資格情報": "発行承認": 承認の失敗によるエラー。</li><li>"発行委任": 発行委任エラーによるエラー。</li><li>"トークンの承認": サード パーティの ID プロバイダーからのトークンを ADFS が拒否したことによるエラー。</li><li>"プロトコル": プロトコル エラーによるエラー。</li><li>"不明": あらゆるものに対応します。定義済みのカテゴリに分類されない、その他すべてのエラー。</li> |
| | サーバー | サーバーに基づいて、エラーをグループ化します。サーバー間でのエラー分布を把握するのに役立ちます。分布が均等でない場合は、サーバーが障害のある状態であることを示す可能性があります。 |
| | ネットワークの場所 | 要求のネットワークの場所 (イントラネットまたはエクストラネット) に基づいて、エラーをグループ化します。エラーになる要求の種類を把握するのに役立ちます。 |
| | アプリケーション | ターゲット アプリケーション (証明書利用者) に基づいて、エラーをグループ化します。エラーの数が最も多いターゲット アプリケーションを把握するのに役立ちます。 |
| ユーザー数: システムでアクティブな一意のユーザー数の平均 | すべて | 選択したタイム スライスにフェデレーション サービスを使用するユーザー数の平均を示します。ユーザーはグループ化されません。<br>平均は、選択したタイム スライスによって異なります。 |
| | アプリケーション | ターゲット アプリケーション (証明書利用者) に基づいて、ユーザー数の平均をグループ化します。これは、どのアプリケーションを何人のユーザーが使用しているかを把握するのに役立ちます。 |

## 参照トピック
Azure AD Connect Health の使用を開始するには、「[Azure AD Connect Health の要件](active-directory-aadconnect-health-requirements.md)」を参照してください。エージェントをインストール済みでデータを収集している場合は、「[Azure AD Connect Health の操作](active-directory-aadconnect-health-operations.md)」を参照してください。Azure AD Connect Health の構成の詳細については、[FAQ](active-directory-aadconnect-health-faq.md) を確認してください。


**その他のリソース**

* [MSDN の Azure AD Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx)


 

<!---HONumber=July15_HO5-->
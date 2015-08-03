<properties
	pageTitle="Operational Insights のセキュリティ"
	description="Operational Insights でお客様のプライバシーを保護し、データをセキュリティで保護する方法について説明します。"
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="dev-center-name"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/02/2015"
	ms.author="banders"/>

# オペレーション インサイトのセキュリティ

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft は、組織の IT インフラストラクチャの管理に役立つソフトウェアやサービスを提供すると同時に、お客様のプライバシーとデータの保護に努めています。他者にデータを委託する場合、信頼には厳格なセキュリティが必要であることを把握しております。Microsoft ではコーディングからサービスの運用まで、厳密なコンプライアンスとセキュリティのガイドラインに準拠しています。

Microsoft においてデータの保護は最優先事項になります。セキュリティ ポリシーを含め、次の情報に関するご質問やご提案、問題がある場合は、「[Azure のサポート オプション](http://azure.microsoft.com/support/options/)」をご覧ください。

この記事は、Microsoft Azure オペレーション インサイトにおけるデータの収集、処理および保護の方法について説明します。いずれかのエージェントを使用して Web サービスに直接接続するか、System Center Operations Manager を使用してオペレーション インサイト サービスの運用データを収集できます。収集したデータはインターネット経由で Microsoft Azure でホストされている、オペレーション インサイト サービスに送信されます。

オペレーション インサイト サービスでは、次のメソッドを使用してデータを安全に管理します。

**データの分離:** 顧客データは Operational Insights サービスを通じて各コンポーネントで論理的に個別に保存されます。すべてのデータは組織ごとにタグ付けされます。このタグ付けはデータのライフ サイクルにおいて継続され、サービスの各層で強制されます。

各顧客には、長期的なデータを格納する専用の Azure Blob が提供されます。Blob は、顧客ごとの一意のキーで暗号化されます。暗号は 90 日ごとに変更されます。

**データの保存**: 一部のソリューションの容量管理などの集計メトリックス (旧称インテリジェンス パック) は Microsoft Azure によってホストされる SQL Database に格納されます。このデータは 390 日間保存されます。インデックス設定済みのログの検索データは、料金プランに従って保持されます。詳細については、「[料金のページ](http://azure.microsoft.com/pricing/details/operational-insights/)」を参照してください。

**物理的なセキュリティ:** Operational Insights サービスは Microsoft の担当者によって運営されており、すべてのアクティビティ ログは記録され、監査することができます。Operational Insights サービスはすべて Azure で実行され、Azure の一般的なエンジニアリングの条件に準拠しています。Azure の資産の物理的なセキュリティに関する詳細は、「[Microsoft Azure セキュリティの概要](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf)」の 18 ページを参照してください。

**準拠と認定資格:** Operational Insights ソフトウェアの開発とサービスのチームは、Microsoft の法律および法令遵守チーム、その他の業界パートナーと積極的に連携し、ISO を含むさまざまな認定資格を取得しています。

現在は次のセキュリティ標準を満たしています。

- Windows の一般的なエンジニアリング条件
- Microsoft の信頼できるコンピューティング証明


## データ フローのセキュリティ
次の図では、会社からの情報の流れと、Operational Insights サービスに移動する際のセキュリティ保護の方法と最終的Operational Insights で表示されるまでを示しています。各手順の詳細は図の後で説明します。

![Operational Insights のデータ収集とセキュリティのイメージ](./media/operational-insights-security/security.png)

### 1.Operational Insights にサインアップし、データを収集する

組織から Operational Insights にデータを送信する場合、Web サービスに直接接続している場合は Microsoft Monitoring Agent を構成するか、Operations Manager のオペレーション コンソールで構成ウィザードを使用する必要があります。ユーザー (自分自身、別の個別ユーザー、グループ) は、Operational Insights アカウントを 1 つ以上作成し、それぞれを直接接続したエージェントに登録するか、次のアカウントのいずれかを使用して Operations Manager 環境に登録する必要があります。


- [組織 ID](../sign-up-organization.md)

- [Microsoft アカウント - Outlook、Office Live、MSN](../sign-up-organization.md)

Operational Insights アカウントは、データが収集、集計、分析、表示される場所になります。Operational Insights アカウントは、主にデータのパーティション分割するための手段として使用されます。Operational Insights の各アカウントは一意になります。たとえば、実稼働データを Operational Insights アカウントの 1 つで管理し、テスト データを別のアカウントで管理する場合があります。アカウントは、ユーザーのデータへのアクセスを管理者が制御する際にも役立ちます。Operational Insights の各アカウントは、アカウントごとに複数のユーザーを関連付けることができます。また、各ユーザー アカウントは複数の Operational Insights アカウントを持つことができます。

構成ウィザードが完了したら、各 Operations Manager 管理グループは、Operational Insights サービスとの接続を確立します。その後、Add Computers (コンピューターの追加) ウィザードを使用します。ウィザードではサービスへのデータの送信が許可される管理グループを選択します。

どちらの種類のエージェントでも Operational Insights のデータを収集します。収集されるデータの種類は、使用するソリューションの種類によって異なります。ソリューションは、定義済みビュー、ログ検索クエリ、データの収集ルール、処理ロジックのバンドルになります。Operational Insights の管理者だけが Operational Insights を使用してソリューションをインポートできます。ソリューションはインポート後、Operations Manager 管理サーバー (使用する場合) に移動し、その後 選択した Operations Manager エージェントに移動されます。その後、エージェントはデータを収集します。

次のテーブルでは、Operational Insights で利用できるソリューションと、収集するデータの種類を示しています。

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>ソリューション</b></td>
		<td><b>データの種類</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>構成の評価</td>
		<td>構成データ、メタデータ、状態データ</td>
    </tr>
    <tr align="left" valign="top">
		<td>容量計画</td>
		<td>パフォーマンス データ、メタデータ、状態データ</td>
    </tr>
	<tr align="left" valign="top">
		<td>マルウェア対策</td>
		<td>構成データ、メタデータ、状態データ</td>
    </tr>
	    <tr align="left" valign="top">
		<td>システムの更新の評価</td>
		<td>構成データと状態のデータ</td>
    </tr>
    <tr align="left" valign="top">
		<td>ログの管理</td>
		<td>ユーザー定義のイベント ログ</td>
    </tr>
    <tr align="left" valign="top">
		<td>変更の追跡</td>
		<td>ソフトウェア インベントリと Windows サービスのメタデータ</td>
    </tr>
    <tr align="left" valign="top">
		<td>SQL と Active Directory の評価</td>
		<td>WMI データ、レジストリ データ、パフォーマンス データ、SQL Server の動的管理の表示結果</td>
    </tr>
    </tbody>
    </table>


次のテーブルでは、データの種類の例を示しています。

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>データの種類</b></td>
		<td><b>フィールド</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>アラート:</td>
		<td>アラート名、アラートの説明、BaseManagedEntityId、問題 ID、IsMonitorAlert、RuleId、ResolutionState、優先度、重大度、カテゴリ、所有者、ResolvedBy、TimeRaised、TimeAdded、LastModified、LastModifiedBy、LastModifiedExceptRepeatCount、TimeResolved、TimeResolutionStateLastModified、TimeResolutionStateLastModifiedInDB、RepeatCount</td>
    </tr>
    <tr align="left" valign="top">
		<td>構成</td>
		<td>CustomerID、AgentID、EntityID、ManagedTypeID、ManagedTypePropertyID、CurrentValue、ChangeDate</td>
    </tr>
	<tr align="left" valign="top">
		<td>イベント</td>
		<td>EventId、EventOriginalID、BaseManagedEntityInternalId、RuleId、PublisherId、PublisherName、FullNumber、番号、カテゴリ、ChannelLevel、LoggingComputer、EventData、EventParameters、TimeGenerated、TimeAdded<p><b>注:</b> カスタム フィールドのイベントを Windows のイベント ログで記録する場合、イベント ログは Operational Insights で収集されます。</td>
    </tr>
	    <tr align="left" valign="top">
		<td>Metadata</td>
		<td>BaseManagedEntityId、ObjectStatus、OrganizationalUnit、ActiveDirectoryObjectSid、PhysicalProcessors、 NetworkName、IPAddress、ForestDNSName、NetbiosComputerName、VirtualMachineName、LastInventoryDate、HostServerNameIsVirtualMachine、IP Address、NetbiosDomainName、LogicalProcessors、DNSName、DisplayName、DomainDnsName、ActiveDirectorySite、PrincipalName、OffsetInMinuteFromGreenwichTime</td>
    </tr>
    <tr align="left" valign="top">
		<td>パフォーマンス</td>
		<td>ObjectName、CounterName、PerfmonInstanceName、PerformanceDataId、PerformanceSourceInternalID、SampleValue、TimeSampled、TimeAdded</td>
    </tr>
    <tr align="left" valign="top">
		<td>状態</td>
		<td>StateChangeEventId、StateId、NewHealthState、OldHealthState、コンテキスト、TimeGenerated、TimeAdded、StateId2、BaseManagedEntityId、MonitorId、HealthState、LastModified、LastGreenAlertGenerated、DatabaseTimeModified</td>
    </tr>
    </tbody>
    </table>


### 2.エージェントからデータを送信する

Web サービスに直接接続しているエージェントをキーで登録します。ポート 443 を使用してエージェントと Operational Insights サービスの間にセキュリティで保護された接続を確立します。

Operations Manager では、Operational Insights サービスにアカウントを登録します。ポート 443 を使用して Operations Manager 管理サーバーと、Operational Insights サービスの間にセキュリティで保護された HTTPS 接続を確立します。Operations Manager が何らかの理由でサービスと通信できない場合、収集したデータは一時的なキャッシュに格納されます。管理サーバーでは 2 時間にわたっての 8 分ごとにデータの再送信を試みます。収集されたデータは圧縮され、負荷を追加しないようにオンプレミス データベースをバイパスして、Operational Insights サービスに送信されます。収集したデータが送信されると、データはキャッシュから削除されます。

### 3.Operational Insights サービスでデータを受信して処理する

Operational Insights サービスにより、証明書とデータの整合性を検証することによって入力されるデータは信頼できる発行元からのものであることを確認します。未処理の生データはその後、BLOB として [Microsoft Azure Storage](http://azure.microsoft.com/documentation/services/storage/) で保存されます。Operational Insights の各ユーザーにはそのユーザーのみがアクセスできる専用の Azure BLOB があります。保存されているデータの種類は、インポートしてデータを収集するために使用したソリューションの種類によって異なります。

Operational Insights サービスによって生データは処理され、集計処理されたデータは、SQL Database に保存されます。Operational Insights サービスと SQL Database 間の通信は、SQL Database の認証に依存します。

### 4.Operational Insights を使用して、データにアクセスする

Operational Insights へは、以前に設定したアカウントを使用してサインインできます。Operational Insights と Operational Insights サービス間のすべてのトラフィックはセキュリティで保護された HTTPS チャネルを介して送信されます。

<!---HONumber=July15_HO4-->
<properties
    pageTitle="Azure Government のドキュメント | Microsoft Azure"
    description="Azure Government アプリケーションの機能の比較と開発におけるガイダンスを示します。"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="scooxl"
    manager="zakramer"
    editor=""/>
<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/05/2016"
    ms.author="scooxl"/>

#  <a name="azure-government-management-and-security"></a>Azure Government の管理とセキュリティ
##  <a name="key-vault"></a>Key Vault
このサービスとその使用方法ついて詳しくは、「<a href="https://azure.microsoft.com/documentation/services/key-vault">Azure Key Vault のパブリック ドキュメント</a>」をご覧ください。
### <a name="data-considerations"></a>データに関する考慮事項
以下の情報は、Azure Key Vault に関する Azure Government の機能領域について記述したものです。

| 許可される規制対象データ | 許可されない規制対象データ |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Key Vault キーで暗号化されるすべてのデータには、規制対象データが含まれていてもかまいません。 | Azure Key Vault のメタデータに、輸出規制対象データを含めることは許可されません。 このメタデータには、Key Vault を作成したり管理したりする際に入力するあらゆる構成データが含まれます。  リソース グループ名、Key Vault 名、サブスクリプション名の各フィールドには規制対象データを入力しないでください。 |

Key Vault は、Azure Government で一般提供されています。 パブリックと同様、拡張機能は存在しません。Key Vault の利用は、PowerShell と CLI のみとなります。
## <a name="log-analytics"></a>Log Analytics
Log Analytics は、Azure Government で一般提供されています。 

### <a name="differences-from-public-azure"></a>パブリック Azure との違い

次の Log Analytics の機能とソリューションは、Azure Government では現在使用できません。 この一覧は、機能/ソリューションの状態が変更された時点で更新されます。

+ パブリック Azure のプレビュー段階のソリューション。これには以下が含まれます。
  - ネットワーク監視ソリューション
  - Azure Networking Analytics ソリューション
  - Office 365 ソリューション
  - Windows 10 アップグレード分析ソリューション
  - アプリケーション間の依存関係の監視
  - Application Insights
  - Azure アクティビティ ログ
  - Azure Automation Analytics
  - Key Vault Analytics
+ Azure Automation を必要とするソリューションと機能。これには以下が含まれます。
  - 更新管理
  - 変更管理
  - Azure Automation Runbook をトリガーするアラート
+ オンプレミスのソフトウェアを更新する必要のあるソリューションと機能
  - System Center Operations Manager 2016 との統合
  - System Center Configuration Manager のコンピューター グループ
  - Surface Hub ソリューション
+ パブリック Azure のプレビュー段階の機能。これには、以下のようなものがあります。
  - PowerBI へのデータのエクスポート
+ Azure ポータルの統合
  - 監視する Azure ストレージ アカウントを選択するには、PowerShell または Resource Manager テンプレートを使用する必要があります
  - Log Analytics エージェントを有効にする仮想マシンを選択するには、PowerShell または Resource Manager テンプレートを使用する必要があります
  - Azure メトリックと Azure 診断
+ OMS モバイル アプリケーション
+ OMS Linux エージェントの VM 拡張機能
+ 使用状況データ

次の Log Analytics 機能は、Azure Government では動作が異なります。

+ Windows エージェントは、Azure Government の [Log Analytics ポータル](https://oms.microsoft.us)からダウンロードする必要があります。
+ データ コレクター API を使用してデータをアップロードするには、Azure Government の URL、https://*workspaceId*.ods.opinsights.azure.us を使用する必要があります。ここで、*workspaceId* は OMS ポータルのワークスペース ID です。 
+ System Center Operations Manager 管理サーバーを Log Analytics に接続するには、更新された管理パックをダウンロードしてインポートする必要があります。
  1. [更新された管理パック](http://go.microsoft.com/fwlink/?LinkId=828749)をダウンロードして保存します。
  2. ダウンロードしたファイルを解凍します。
  3. 管理パックを Operations Manager にインポートします。 ディスクから管理パックをインポートする方法については、ディスクの管理の詳細については、Microsoft TechNet Web サイトの「[How to Import an Operations Manager Management Pack](http://technet.microsoft.com/library/hh212691.aspx)」(Operations Manager 管理パックのインポート方法) をご覧ください。
  4. Operations Manager を Log Analytics に接続するには、「[Operations Manager を Log Analytics に接続する](../log-analytics/log-analytics-om-agents.md)」の手順に従ってください。 



### <a name="frequently-asked-questions"></a>よく寄せられる質問

+ パブリック Azure の Log Analytics のデータを Azure Government に移行できますか。
  - いいえ。 パブリック Azure のデータやワークスペースを Azure Government に移動することはできません。
+ OMS Log Analytics ポータルから、パブリック Azure と Azure Government のワークスペースを切り替えることができますか。
  - いいえ。 パブリック Azure と Azure Government のポータルは別個であり、情報を共有することはできません。 

詳細については、 [Log Analytics のパブリック ドキュメント](../log-analytics/log-analytics-overview.md)をご覧ください。

## <a name="next-steps"></a>次のステップ

補足情報と最新情報については、<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government ブログ</a>を講読してください。
 



<!--HONumber=Oct16_HO2-->



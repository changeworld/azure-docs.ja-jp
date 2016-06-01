<properties 
   pageTitle="Azure App Service で取引先契約を作成する | Microsoft Azure" 
   description="取引先契約の作成" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
	ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>

# 取引先契約の作成   

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

取引先は、B2B (企業間) コミュニケーションに含まれるエンティティです。2 つのパートナーがある関係を確立した場合、この関係は*契約*と呼ばれます。契約は、2 つのパートナーが達成したいと考えているコミュニケーションに基づいて定義され、プロトコルまたはトランスポートに固有のものです。Azure App Service がサポートするさまざまな B2B プロトコルとトランスポートには、以下のものが含まれます。

- AS2 (Applicability Statement 2)
- EDIFACT (United Nations/Electronic Data Interchange For Administration, Commerce and Transport (UN/EDIFACT))
- X12 (ASC X12)

### B2B のシナリオをサポートする BizTalk API Apps
BizTalk API Apps には、次のようなものがあります。Azure ポータルの豊富で直感的な操作性を使用したそれぞれの機能を列挙します。


## BizTalk 取引先管理 (TPM)
- パートナー、プロファイル、ID の作成と管理
- EDI スキーマの格納と管理
- 証明書 (AS2 プロトコルで使用) の格納と管理
- AS2 契約の作成と管理
- EDIFACT 契約の作成と管理 (送信側バッチ処理を含む)
- X12 契約の作成と管理 (送信側バッチ処理を含む)

![][1]


## AS2 コネクタ
- 関連する TPM API アプリ インスタンスで定義された AS2 契約を実行します。
- トラブルシューティングのために、AS2 の処理情報と追跡情報を表示します。


## BizTalk EDIFACT
- 関連する TPM API アプリ インスタンスで定義された EDIFACT 契約を実行します。
- トラブルシューティングのために、EDIFACT の処理情報と追跡情報を表示します。
- 関連の TPM API アプリ インスタンスの EDIFACT 契約で定義されたバッチの状態管理 (開始、停止) を行います。


## BizTalk X12
- 関連する TPM API アプリ インスタンスで定義された X12 契約を実行します。 
- トラブルシューティングのために、X12 の処理情報および追跡情報を表示します。
- 関連の TPM API アプリ インスタンスで X12 契約で定義されたバッチの状態管理 (開始、停止) を行います。

既に説明したとおり、AS2、X12、および EDIFACT の API Apps が期待どおりの機能を果たすためには TPM API Apps が必要です。


## Getting Started (概要)
取引先契約を作成するには、次の手順に従います。

1. **BizTalk 取引先管理**コネクタを作成します。これには、正常に機能する空の SQL Database が必要です。開始する前に、利用できる空のデータベースがあり、使用準備ができていることを確認します。
2. 契約に必要なスキーマと証明書をアップロードします。この作業は、作成済みの TPM インスタンスを参照し、"スキーマ" パートまたは "証明書" パートにステップインすることにより実行します。
3. 作成済みの TPM インスタンスを参照し、**パートナー** パートにステップインします。
4. 必要なパートナーを作成します。また、必要に応じてプロファイルを編集し、必要な ID を追加します。
5. ここからは、契約を作成するために**契約**パートを使用します。契約を作成する際に、使用されるプロトコルを選択する必要があります。残りの構成オプションは、選択したプロトコルに基づきます。

![][2]

![][3]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-trading-partner-agreement/TPMResourceView.png
[2]: ./media/app-service-logic-create-a-trading-partner-agreement/ProtocolSelection.png
[3]: ./media/app-service-logic-create-a-trading-partner-agreement/X12AgreementCreation.png
 

<!------HONumber=AcomDC_0420_2016-->
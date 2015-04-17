<properties 
   pageTitle="VETR を使用した EAI ロジック アプリの作成" 
   description="このトピックでは、BizTalk XML サービスの検証、エンコード、変換の各機能について説明します。" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/23/2015"
   ms.author="rajram"/>


# VETR を使用した EAI ロジック アプリの作成

ほとんどのエンタープライズ アプリケーション統合 (EAI) シナリオでは、ソースと出力先との間でデータの仲介をします。このようなシナリオには、多くの場合、次のような共通の要件があります。

- 異なるシステムから受け取るデータが正しい形式であることを確認する。
- 受信データに対して "ルックアップ" を実行して意思決定を行う。
- ある形式から別の形式 (たとえば、CRM システムのデータ形式から ERP システムのデータ形式) にデータを変換する。
- 目的のアプリケーションまたはシステムにデータを伝送するルートを作成する。

この記事では、一般的な統合パターンの"一方向メッセージ仲介" または VETR (検証、強化、変換、ルート) と呼ばれるパターンについて説明します。VETR パターンは、ソース エンティティと、出力先エンティティとの間でデータを仲介します。通常は、ソースも出力先もデータ ソースです。 

注文を受け付ける Web サイトの場合を考えます。ユーザーは、HTTP を使用してシステムに注文をポストします。その間、システムは受信データが正確かどうかを検証したうえで正規化し、次の処理のために Service Bus キューに保持します。システムは、キューから注文を取り出し、特定の形式で待ち受けます。このため、エンド ツー エンドのフローは、以下のようになります。

HTTP -> 検証 -> 変換 -> Service Bus

![Basic VETR Flow][1]

次の BizTalk API Apps は、このパターンを作成する際に役立ちます。

*HTTP トリガー* - メッセージ イベントをトリガーするソース。
*検証* - 受信データが正確であるかどうかを検証する。
*変換* - データを受信形式から下流システムで必要な形式に変換する。
*Service Bus コネクタ* - データを送信する出力先エンティティ。


## 基本的な VETR パターンの構築
### 基本操作

Azure 管理ポータルで画面の左下の **[+ 新規]** をクリックし、[ロジック アプリ] をクリックします。名前、場所、サブスクリプション、リソース グループ、および動作する場所を選択します。リソース グループは、アプリのコンテナーとして機能します。また、アプリのリソースはすべて同じリソース グループに移動します。

次に、トリガーとアクションを追加します。


## HTTP トリガーの追加

1. ギャラリーから **[HTTP リスナー]** を選択して新しいリスナーを作成します。このリスナーを **HTTP1** と呼びます。
2. **[応答を自動的に送信しますか?]** の設定は、[いいえ] のままにしておきます。_HTTP メソッド_を _[投稿]_ に、_相対 URL_ を _[/OneWayPipeline]_ に設定して、トリガーのアクションを構成します。

![HTTP Trigger][2]


## 検証アクションの追加

ここでは、トリガーが起動したとき (HTTP エンドポイントで呼び出しが受信されたとき) に常に実行されるアクションを入力します。

1. ギャラリーから **[BizTalk XML Validator]** を追加し、_(Validate1)_ という名前を付けてインスタンスを作成します。
2. 受信 XML メッセージを検証する XSD スキーマを構成します。_[検証]_ アクションを選択し、InputXml パラメーターの値として _[triggers('httplistener').outputs.Content]_ を選択します。

これで、検証アクションは、HTTP リスナー後の最初のアクションになります。同様にして、他のアクションも追加します。

![BizTalk XML Validator][3]


## 変換アクションの追加
受信データを正規化するために変換を構成します。

1. ギャラリーから **[変換]** を追加します。 
2. 変換を構成して受信 XML メッセージを変換するためには、この API が呼び出されたときに実行するアクションとして**変換**アクションを選択します。次に、_inputXml_ の値として ```triggers('httplistener').outputs.Content``` を選択します。受信データは、構成済みのすべての変換に適合しますが、スキーマに一致するものだけが適用されるため、Map はオプションのパラメーターです。
3. 最後に、検証が成功した場合にのみ、変換が実行されるようにします。この条件を構成するには _[条件を追加する]_ の右上にある歯車アイコンを選択します。条件を ```equals(actions('xmlvalidator').status,'Succeeded')``` に設定します。


![BizTalk Transforms][4]


## Service Bus コネクタの追加
次に、データを書き込む場所である出力先 (Service Bus キュー) を追加します。

1. ギャラリーから **Service Bus コネクタ**を追加します。_[名前]_ を _Servicebus1_ に、_[接続文字列]_ をユーザーのサービス バス インスタンスへの接続文字列に、_[エンティティ名]_ を _[キュー]_ に設定します。_[サブスクリプション名]_ はスキップします。 
2. **メッセージの送信**アクションを選択し、このアクションの _[メッセージ]_ フィールドを _actions('transformservice').outputs.OutputXml_ に設定します。

![Service Bus][5]


## HTTP 応答の送信
パイプライン処理の完了後、成功した場合と失敗した場合のそれぞれについて HTTP 応答を返すように構成します。手順は次のとおりです。

1. ギャラリーから **HTTP リスナー**を追加し、**HTTP 応答の送信**アクションを追加します。
2. _[応答コンテンツ]_ を "パイプライン処理が完了しました" に設定し、HTTP 200 が OK であることを示すために _[応答状態コード]_ を "200" に設定します。また、式の条件は、```@equals(actions('servicebusconnector').status,'Succeeded')``` に設定します。
	
失敗した場合についても、上記と同じ手順を繰り返すことにより HTTP 応答を送信します。ただし、式の条件を ```@not(equals(actions('servicebusconnector').status,'Succeeded'))```` に変更します。


## 完了
メッセージが HTTP エンドポイントに送信されるたびに、エンドポイントがアプリをトリガーし、上記で作成したアクションを実行します。このようにして作成したロジック アプリを管理するには、Azure の管理ポータルで **[参照]** をクリックし、さらに **[ロジック アプリ]** をクリックします。確認したいアプリをクリックすると、詳細が表示されます。


<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG


<!--HONumber=49-->
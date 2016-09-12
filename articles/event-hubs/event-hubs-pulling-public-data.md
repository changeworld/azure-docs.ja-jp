<properties
    pageTitle="パブリック データの Azure Event Hubs へのプル | Microsoft Azure"
    description="Web サンプルから Event Hubs へのインポートの概要"
    services="event-hubs"
    documentationCenter="na"
    authors="spyrossak"
    manager="timlt"
    editor=""/>

<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/25/2016"
    ms.author="spyros;sethm" />

# パブリック データの Azure Event Hubs へのプル

モノのインターネット (IoT) の一般的なシナリオでは、データを Azure にプッシュするプログラムを記述できるデバイスが含まれています。プッシュ先には、Azure Event Hub と IoT ハブがあります。これらのハブはどちらも、Microsoft Azure で利用できる多種多様なツールによって格納、分析、およびビジュアル化を行うための Azure へのエントリ ポイントです。ただし、どちらも、データを JSON としてフォーマットし、特定の方法で保護する必要があります。これにより、次の問題が提起されます。データが何らかの種類の Web ソースやフィードとして公開されているパブリックまたはプライベート ソースからデータをプッシュしたいが、データの公開方法を変更できない場合はどうすればよいのでしょうか。 気象情報、交通情報、株価情報を考えてください。Event Hub へのプッシュを構成するときに、NOAA であるか WSDOT であるか NASDAQ であるかを知ることはできません。この問題を解決するために、このようなソースからデータをプルして Event Hub にプッシュするオープン ソースの小さなクラウド サンプルが用意されています。開発者は、このサンプルを修正してデプロイできます。開発者は、このサンプルを改変して、したいことを何でも実行できます。ただし、発行元のライセンス条件に従うことは言うまでもありません。[ここ](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/)でそのアプリケーションを見つけることができます。

このサンプルのコードは、一般的な Web フィードからデータをプルする方法と、Azure Event Hub にデータを書き込む方法のみを示していることに注意してください。これは、運用アプリケーションではなく、運用環境で使用できるものではありません。あくまでも、開発者が独自のアプリケーションを記述するために利用できるサンプルとして提供されています。さらに、このサンプルの存在は、データを Azure に**プッシュ**するのではなく、**プル**すべきであるということを推奨するものではありません。開発者は、エンド ツー エンド アプリケーションを設定する前に、セキュリティ、パフォーマンス、機能、およびコストの各要素を検討する必要があります。

## アプリケーション構造

アプリケーションは C# で記述され、このアプリケーションを変更、ビルド、および発行するために必要なすべての情報が[サンプルの説明](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/)に含まれています。次のセクションでは、アプリケーションの動作の概要を説明します。

データ フィードにアクセスできることを前提として説明を始めます。たとえば、ワシントン州交通局が提供する交通情報や NOAA が提供する気象データをプルして、カスタム レポートに表示したり、アプリケーション内の他のデータと組み合わせたりするとします。Azure Event Hub をセットアップし、それにアクセスするために必要な接続文字列を知っておく必要もあります。

GenericWebToEH ソリューションが起動すると、ソリューションは構成ファイル (App.config) を読み取ってさまざまな情報を取得します。

1. データを発行しているサイトの URL または URL の一覧。理想的には、これは WSDOT によって参照されているサイト ([こちら](http://www.wsdot.wa.gov/Traffic/api/)) のような、データを JSON で発行しているサイトです。
2. URL の資格情報 (必要な場合)。多くのパブリック ソースでは資格情報は必要ありません。資格情報は URL 文字列に配置できます。一部のソースでは、個別に提供する必要があります (このアプリケーションでは資格情報セットを 1 つだけ指定できます。したがって、アプリケーションは URL の一覧ではなく、URL を 1 つだけ指定した場合にのみ機能することに注意してください)。
3. 接続文字列と Event Hubs 名前空間内のデータをプッシュする Event Hub の名前。この情報は、Azure ポータルで見つけることができます。
4. パブリック データ サイトのポーリング間のスリープ間隔 (ミリ秒単位)。この設定には配慮が必要です。ポーリング回数が少なすぎると、適切なデータが取得できない可能性があります。反対に、ポーリング回数が多すぎると、同じデータを何度も取得する可能性があります。さらに悪いことに、不正なボットとしてブロックされることがあります。データ ソースを更新する頻度を検討してください。気象や交通のデータは 15 分ごとに、株価はおそらく数秒ごとに更新できる可能性がありますが、頻度はデータの取得先によって異なります。
5. データが JSON または XML のどちらで送信されるかをアプリケーションに通知するフラグ。データを Event Hub にプッシュする必要があるため、アプリケーションには、送信する前に XML を JSON に変換するモジュールがあります。

構成ファイルを読み取った後、アプリケーションはループに入ります。パブリック web サイトにアクセスし、必要に応じてデータを変換し、Event Hub にデータを書き込み、スリープ間隔の間待機した後、すべてを繰り返します。具体的には次の処理が行われます。

  * パブリック Web サイトを読み取ります。すぐに送信できるデータを受信した場合は、Azure/GenericWebToEH/ApiReaders/RawXMLWithHeaderToJsonReader.cs の RawXMLWithHeaderToJsonReader クラスのインスタンスが使用されます。GetData() メソッドのソース ストリームを読み取り、GetXmlFromOriginalText を使用して小さなピース (= レコード) に分割します。このメソッドは、XML も整形式 JSON も JSON アレイも読み取ります。その後、App.config (default=empty) の MergeToXML 構成を使用する処理が開始されます。
  * データの送受信は、Program.cs の Process() メソッド内のループとして実装されます。GetData() から出力結果を受信した後、メソッドは、分離された値を Event Hub にエンキューします。

## 次のステップ

ソリューションをデプロイするには、[GenericWebToEH](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) アプリケーションを複製するかダウンロードし、App.config ファイルを編集し、アプリケーションをビルドし、最後に発行します。アプリケーションを発行した後、Cloud Services の Azure クラシック ポータルでそれが実行されていることを確認でき、**[構成]** タブで構成設定の一部 (Event Hub ターゲットやスリープ間隔など) を変更できます。

[Azure サンプル ギャラリー](https://azure.microsoft.com/documentation/samples/?service=event-hubs)と [MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5) で、他の Event Hubs のサンプルをご覧ください。

<!---HONumber=AcomDC_0831_2016-->
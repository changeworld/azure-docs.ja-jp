
<properties
	pageTitle="Logic Apps 向けの API アプリの拡張"
	description="この記事では、Logic Apps に API アプリを対応させるための方法について説明します"
	services="app-service\api"
	documentationCenter=".net"
	authors="sameerch"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2015"
	ms.author="sameerch;guayan;tarcher"/>

# Logic Apps 向けの API アプリの拡張 #

この記事では、API アプリを Logic Apps に対応させるための API 定義を定義する方法について説明します。これにより、Logic Apps デザイナーで使用したときの API アプリのエンド ユーザー エクスペリエンスが向上します。

## 前提条件

[Azure App Service](../app-service/app-service-value-prop-what-is.md) の [API アプリ](app-service-api-apps-why-best-platform.md)を初めて使用する場合は、[API アプリの作成](app-service-dotnet-create-api-app.md)に関する一連のページを読むことをお勧めします。


## 表示名の追加 ##
Logic Apps デザイナーには操作、フィールド、パラメーターの名前が表示されますが、これらはプログラムによって生成された名前のため読みづらいことがあります。読みやすさを高めるために、Logic Apps デザイナーでは、*表示名*と呼ばれるより読みやすいテキスト値を使用できる場合に、操作、フィールド、パラメーターの既定名に代えて表示名を表示できます。これを実現するために、Logic Apps デザイナーは、API アプリによって提供される swagger メタデータ内に特定のプロパティがあるかどうかを調べます。表示名として、次のプロパティが使用されます。

* 操作 (アクションやトリガー)。**summary** プロパティがある場合はその値、それ以外の場合は **operationId** プロパティの値。Swagger 2.0 仕様では、**summary** プロパティに最大 120 文字を使用できます。

* パラメーター (入力)。**x-ms-summary** 拡張プロパティがある場合はその値、それ以外の場合は **name** プロパティの値。**x-ms-summary** 拡張プロパティは、コードで動的に設定する必要があります。その方法については、このトピックの「カスタム属性を使用した拡張プロパティの注釈の設定」セクションで説明します。**name** プロパティは、/// コメントを使用して設定できます。その方法については、このトピックの「API 定義の生成時の XML コメントの使用」セクションで説明します。

* スキーマ フィールド (出力応答)。**x-ms-summary** 拡張プロパティがある場合はその値、それ以外の場合は **name** プロパティの値。**x-ms-summary** 拡張プロパティは、コードで動的に設定する必要があります。その方法については、このトピックの「カスタム属性を使用した拡張プロパティの注釈の設定」セクションで説明します。**name** プロパティは、/// コメントを使用して設定できます。その方法については、このトピックの「API 定義の生成時の XML コメントの使用」セクションで説明します。

**注:** 表示名の長さは 30 文字以下にすることをお勧めします。

### API 定義の生成時の XML コメントの使用

一般的に、Visual Studio を使用した開発では、[XML コメント](https://msdn.microsoft.com/library/b2s063f7.aspx)を使用して API コントローラーに注釈を付けます。[/doc](https://msdn.microsoft.com/library/3260k4x7.aspx) を使用してコンパイルすると、コンパイラは、XML ドキュメント ファイルを作成します。API アプリ SDK に含まれている Swashbuckle ツールセットでは、API メタデータを生成するときにこれらのコメントを組み込むことができます。この操作を実現するには、次の手順に従って API プロジェクトを構成します。

1. Visual Studio でプロジェクトを開きます。

2. **ソリューション エクスプローラー**で、プロジェクトを右クリックし、**[プロパティ]** をクリックします。

	![Project Properties](./media/app-service-api-optimize-for-logic-apps/project-properties.png)

3. プロジェクトのプロパティ ページが表示されたら、次の手順を実行します。

	- 設定を適用する**構成**を選択します。通常は、指定した設定がデバッグ ビルドとリリース ビルドの両方に適用されるように、[すべての構成] を選択します。
	
	- 左側の **[ビルド]** タブを選択します。
	
	- **[XML ドキュメント ファイル]** チェック ボックスがオンになっていることを確認します。Visual Studio では、プロジェクトの名前に基づいて既定のファイル名が付けられます。この値は、名前付け規則によって求められる値に設定することも、そのまま使用することもできます。

	![Set XML Doc property](./media/app-service-api-optimize-for-logic-apps/xml-documentation-file-property.png)

4. (プロジェクトの **App\_Start** フォルダーにある) *SwaggerConfig.cs* ファイルを開きます。

5. *SwaggerConfig.cs* ファイルの先頭に、**System** 名前空間と **System.Globalization** 名前空間の **using** ディレクティブを追加します。

		using System;
		using System.Globalization;
 
6. *SwaggerConfig.cs* ファイル内で **GetXmlCommentsPath** の呼び出しを検索し、このメソッドが実行されるように行をコメント解除します。このメソッドはまだ実装されていません。そのため、Visual Studio によって **GetXmlCommentsPath** の呼び出しに下線が付けられ、現在のコンテキストで定義されていないことが示されます。ここでは問題ありません。次の手順でこのメソッドを実装します。

7. 次の **GetXmlCommentsPath** メソッドの実装を (*SwaggerConfig.cs* ファイルに定義されている) **SwaggerConfig** クラスに追加します。このメソッドは、前の手順でプロジェクトの設定に指定した XML ドキュメント ファイルを返すのみです。

        public static string GetXmlCommentsPath()
        {
            return String.Format(CultureInfo.InvariantCulture, 
								 @"{0}\bin\ContactsList.xml", 
								 AppDomain.CurrentDomain.BaseDirectory);
        }

8. 最後に、コントローラー メソッドの XML コメントを指定します。そのためには、API アプリのコントローラー ファイルの 1 つを開き、ドキュメント化するコントローラー メソッドの前の空行に「///」を入力します。Visual Studio により、コメント セクションが自動的に挿入されます。このセクション内に、メソッドの概要、パラメーター、戻り値の情報を指定できます。

API アプリをビルドして発行すると、ドキュメント ファイルがペイロードに含まれ、API アプリの残りの部分と共にアップロードされることを確認できます。

## 高度な操作やプロパティの分類

Logic Apps デザイナー上で操作、パラメーター、プロパティを表示するための領域は限られています。加えて、API アプリでは、多数の操作やプロパティを定義できます。狭い領域に大量の情報を表示すると、デザイナーが使いにくくなります。

Logic Apps デザイナーでは、あふれる情報に対応するために、API アプリの操作とプロパティをユーザー定義のカテゴリにグループ化できます。API アプリは、操作とプロパティの適切な分類を使用して最初に最も基本的な有用な操作やプロパティを表示することにより、ユーザー エクスペリエンスを向上させることができます。

この機能を提供するために、Logic Apps デザイナーは、API アプリの swagger API 定義内で特定のカスタム ベンダー拡張プロパティを検索します。この **x-ms-visibility** プロパティは、次の値を受け取ることができます。

* 空白や "none"。これらの操作とプロパティは、ユーザーが簡単に表示できます。

* "advanced"。これらの操作とプロパティは高度なため、既定では非表示に設定されます。ただし、ユーザーは、必要に応じて簡単にこれらの操作とプロパティにアクセスできます。

* "internal"。これらの操作とプロパティはシステム プロパティや内部プロパティとして扱われ、ユーザーが直接使用することは想定されていません。そのため、これらの操作とプロパティはデザイナーで非表示に設定され、コード ビューでのみ使用できます。このようなプロパティに対しては、**x-ms-scheduler-recommendation** 拡張プロパティを指定して、Logic Apps デザイナーを使用して値を設定することもできます。例については、[API アプリへのトリガーの追加](app-service-api-dotnet-triggers.md)に関するページをご覧ください。


## カスタム属性を使用した拡張プロパティの注釈の設定

既に説明したように、カスタムのベンダー拡張プロパティは、Logic Apps デザイナーが使用できるさまざまな情報を提供する注釈を API メタデータに設定するために使用します。静的メタデータを使用して API アプリを記述する場合は、プロジェクトの */metadata/apiDefinition.swagger.json* を直接編集して、必要な拡張プロパティを手動で追加できます。

動的メタデータを使用する API アプリの場合は、カスタム属性を使用してコードに注釈を設定できます。次に、*SwaggerConfig.cs* ファイルにカスタム属性を検索する操作フィルターを定義し、さらに必要なベンダー拡張を追加します。この方法を使用して **x-ms-summary** 拡張プロパティを動的に生成する手順を次に示します。

1. コードに注釈を設定するための、**CustomSummaryAttribute** という名前の属性クラスを定義します。

	    [AttributeUsage(AttributeTargets.All)]
	    public class CustomSummaryAttribute : Attribute
	    {
	        public string SummaryText { get; internal set; }

	        public CustomSummaryAttribute(string summaryText)
	        {
	            this.SummaryText = summaryText;
	        }
	    }

2. 操作パラメーター内でこのカスタム属性を検索する、**AddCustomSummaryFilter** という名前の操作フィルターを定義します。


	    using Swashbuckle.Swagger;

		...

		public class AddCustomSummaryFilter : IOperationFilter
	    {
	        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
	        {
	            if (operation.parameters == null)
	            {
	                // no parameter
	                return;
	            }

	            foreach (var param in operation.parameters)
	            {
	                var summaryAttributes = apiDescription.ParameterDescriptions.First(x => x.Name.Equals(param.name))
	                                        .ParameterDescriptor.GetCustomAttributes<CustomSummaryAttribute>();

	                if (summaryAttributes != null && summaryAttributes.Count > 0)
	                {
	                    // add x-ms-summary extension
	                    if (param.vendorExtensions == null)
	                    {
	                        param.vendorExtensions = new Dictionary<string, object>();
	                    }
	                    param.vendorExtensions.Add("x-ms-summary", summaryAttributes[0].SummaryText);
	                }
	            }
	        }
	    }

3. *SwaggerConfig.cs* ファイルを編集して、前の手順で定義したフィルター クラスを追加します。


            GlobalConfiguration.Configuration
                .EnableSwagger(c =>
                    {
                        ...
                        c.OperationFilter<AddCustomSummaryFilter>();
                        ...
                    }

4. 次のコード スニペットに示すように、**CustomSummaryAttribute** クラスを使用して、コードに注釈を設定します。

        /// <summary>
        /// Send Message
        /// </summary>
        /// <param name="queueName">The name of the Storage Queue</param>
        /// <param name="messageText">The message text to be sent</param>
        public void SendMessage(
            [CustomSummary("Queue Name")] string queueName,
            [CustomSummary("Message Text")] string messageText)
        {
             ...
        }

	上記の API アプリをビルドすると、次の API メタデータが生成されます。


			...
            "post": {
                ...
                "parameters": [
                    {
                        "name": "queueName",
                        "in": "query",
                        "description": "The name of the Storage Queue",
                        "required": true,
                        "x-ms-summary": "Queue Name",
                        "type": "string"
                    },
                    {
                        "name": "messageText",
                        "in": "query",
                        "description": "The message text to be sent",
                        "required": true,
                        "x-ms-summary": "Message Text",
                        "type": "string"
                    }
                ],
                ...

5. 同様に、**AddCustomSummarySchemaFilter** スキーマ フィルターを定義して、スキーマ モデルの **x-ms-summary** 拡張プロパティに自動的に注釈を付けることができます。次に例を示します。

	    public class AddCustomSummarySchemaFilter: ISchemaFilter
	    {
	        public void Apply(Schema schema, SchemaRegistry schemaRegistry, Type type)
	        {
	            SetCustomSummary(schema, type.GetCustomAttribute<CustomSummaryAttribute>());

	            if (schema.properties != null)
	            {
	                foreach (var property in schema.properties)
	                {
	                    var summaryAttribute = type.GetProperty(property.Key).GetCustomAttribute<CustomSummaryAttribute>();
	                    SetCustomSummary(property.Value, summaryAttribute);
	                }
	            }
	        }

	        private static void SetCustomSummary(Schema schema, CustomSummaryAttribute summaryAttribute)
	        {
	            if (summaryAttribute != null)
	            {
	                if (schema.vendorExtensions == null)
	                {
	                    schema.vendorExtensions = new Dictionary<string, object>();
	                }
	                schema.vendorExtensions.Add("x-ms-summary", summaryAttribute.SummaryText);
	            }
	        }
	    }

## 概要

この記事では、Logic Apps デザイナーで使用したときの API アプリのエンド ユーザー エクスペリエンスを向上させる方法について説明しました。ベスト プラクティスとして、すべての操作 (アクション、トリガー)、パラメーター、プロパティに対して適切なフレンドリ名を付けることをお勧めします。また、基本的な操作については 5 個以下にすることをお勧めします。入力パラメーターについては基本的なプロパティの数を 4 個以下、プロパティについては 5 個以下にすることをお勧めします。残りの操作とプロパティについては、高度な操作とプロパティであることを示すマークを付ける必要があります。
 

<!---HONumber=August15_HO6-->
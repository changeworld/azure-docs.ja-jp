<properties 
   pageTitle="Azure Data Lake Analytics ジョブの U-SQL ユーザー定義演算子の開発 | Azure" 
   description="Data Lake Analytics ジョブで使用および再使用されるユーザー定義演算子の開発方法について説明します。" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/22/2015"
   ms.author="jgao"/>


# Azure Data Lake Analytics ジョブの U-SQL ユーザー定義演算子の開発

Data Lake Analytics ジョブで使用および再使用されるユーザー定義演算子の開発方法について説明します。国名を変換するカスタム演算子を開発します。

##前提条件

- Visual Studio 2015、Visual Studio 2013 update 4、または Visual Studio 2012 (Visual C++ インストール済み)。 
- Microsoft Azure SDK for .NET バージョン 2.5 以上。Web Platform Installer を使用してインストールします。
- Data Lake Analytics アカウント。「[Azure ポータルで Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)」を参照してください。
- 「[Azure Data Lake Analytics U-SQL Studio の使用](data-lake-analytics-u-sql-studio-get-started.md)」チュートリアルを読み進める。
- Azure への接続。「[Azure Data Lake Analytics U-SQL Studio の使用](data-lake-analytics-u-sql-studio-get-started.md#connect-to-azure)」を参照してください。 
- ソース データのアップロード。「[Azure Data Lake Analytics U-SQL Studio の使用](data-lake-analytics-u-sql-studio-get-started.md#upload-source-data-files)」を参照してください。 

## U-SQL でのユーザー定義演算子の定義と使用

**U-SQL ジョブを作成して送信するには**

1. **[ファイル]** メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。
2. **[U-SQL プロジェクト]** タイプを選択します。

	![新しい U-SQL Visual Studio プロジェクト](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. **[OK]** をクリックします。Visual Studio で、Script.usql ファイルを使用してソリューションが作成されます。
4. **ソリューション エクスプ ローラー**で、Script.usql を展開してから **Script.usql.cs** をダブルクリックします。
5. 以下のコードをファイルに貼り付けます。

		using Microsoft.Analytics.Interfaces;
		using System.Collections.Generic;
		
		namespace USQL_UDO
		{
			public class CountryName : IProcessor
			{
				private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
				{
					{
						"Deutschland", "Germany"
					},
					{
						"Schwiiz", "Switzerland"
					},
					{
						"UK", "United Kingdom"
					},
					{
						"USA", "United States of America"
					},
					{
						"中国", "PR China"
					}
				};
		
				public override IRow Process(IRow input, IUpdatableRow output)
				{
		
					string UserID = input.Get<string>("UserID");
					string Name = input.Get<string>("Name");
					string Address = input.Get<string>("Address");
					string City = input.Get<string>("City");
					string State = input.Get<string>("State");
					string PostalCode = input.Get<string>("PostalCode");
					string Country = input.Get<string>("Country");
					string Phone = input.Get<string>("Phone");
		
					if (CountryTranslation.Keys.Contains(Country))
					{
						Country = CountryTranslation[Country];
					}
					output.Set<string>(0, UserID);
					output.Set<string>(1, Name);
					output.Set<string>(2, Address);
					output.Set<string>(3, City);
					output.Set<string>(4, State);
					output.Set<string>(5, PostalCode);
					output.Set<string>(6, Country);
					output.Set<string>(7, Phone);
		
					return output.AsReadOnly();
				}
			}
		}

5. Script.usql を開き、次の U-SQL スクリプトを貼り付けます。

		@drivers =
			EXTRACT UserID      string,
					Name        string,
					Address     string,
					City        string,
					State       string,
					PostalCode  string,
					Country     string,
					Phone       string
			FROM "/Samples/Data/AmbulanceData/Drivers.txt"
			USING Extractors.Tsv(Encoding.Unicode);
		
		@drivers_CountryName =
			PROCESS @drivers
			PRODUCE UserID string,
					Name string,
					Address string,
					City string,
					State string,
					PostalCode string,
					Country string,
					Phone string
			USING new USQL_UDO.CountryName();    
		
		OUTPUT @drivers_CountryName
			TO "/Samples/Outputs/Drivers.csv"
			USING Outputters.Csv(Encoding.Unicode);

6. **ソリューション エクスプローラー**で、**Script.usql** を右クリックしてから **[スクリプトのビルド]** をクリックします。
6. **ソリューション エクスプローラー**で、**Script.usql** を右クリックしてから **[スクリプトの送信]** をクリックします。
7. Azure サブスクリプションに接続していない場合は、Azure アカウント資格情報の入力を求められます。
7. **[Submit]** をクリックします。送信が完了すると、[結果] ウィンドウに送信結果とジョブのリンクが示されます。
8. 最新のジョブの状態を表示して、画面を更新するには、[更新] をクリックする必要があります。

**ジョブの出力を表示するには**

1. **サーバー エクスプ ローラー**で、**[Azure]**、**[Data Lake Analytics]**、[Data Lake Analytics アカウント]、**[ストレージ アカウント]** の順に展開し、既定のストレージを右クリックしてから **[エクスプローラー]** をクリックします。 
2. [サンプル]、[出力] の順にクリックしてから、**Drivers.csv** をダブルクリックします。


##関連項目

- [PowerShell で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-powershell.md)
- [Azure ポータルで Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
- [U-SQL アプリケーションを開発するための Data Lake Tools for Visual Studio の使用](data-lake-analytics-data-lake-tools-get-started.md)

<!---HONumber=AcomDC_1203_2015-->
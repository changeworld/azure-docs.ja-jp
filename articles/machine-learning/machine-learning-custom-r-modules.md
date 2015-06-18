<properties 
	pageTitle="Azure Machine Learning における作成者カスタム R モジュール | Azure" 
	description="Azure Machine Learning における作成者カスタム R モジュールのクイック スタート。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev"  
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="tbd" 
	ms.date="02/04/2015" 
	ms.author="bradsev" />


# Azure Machine Learning における作成者カスタム R モジュール

このトピックでは、Azure Machine Learning におけるカスタム R モジュールを作成する方法について説明します。カスタム R モジュールとは何か、定義するためにどのファイルを使用するか説明します。Machine Learning ワークスペースでのデプロイメントのためにそれらのファイルを作成する方法やモジュールを登録する方法を示します。カスタム モジュールの定義で使用する要素および属性についてさらに詳しく説明します。補助機能とファイルおよび複数の出力を使用する方法も記載されています。 

## カスタム R モジュールとは
カスタム R モジュールは、ユーザーのワークスペースにアップロードでき、Azure Machine Learning の実験の一部として実行できるユーザー定義モジュールです。カスタム R モジュールは、Machine Learning でユーザー定義 R 関数を実行するカスタム モジュールです。R とは、アルゴリズムを実装するために統計学者やデータ科学者によって広く使用されている統計コンピューティンおよびグラフィックス用のプログラミング言語です。Machine Learning 言語サポートは、カスタム モジュールで R 言語を使用するように既定で設定されています。

カスタム モジュールには、他のモジュールと同じように使用できるという意味で Azure Machine Learning のファースト クラス ステータスがあります。公開されたまたは視覚化された実験を含むその他のモジュールとともに実行できます。ユーザーは、モジュールによって実装されたアルゴリズム、使用される入出力ポート、モデリング パラメーター、およびその他さまざまな実行時の動作を制御できます。


## カスタム R モジュール内のファイル
カスタム R モジュールは、少なくとも以下の 2 つのファイルが含まれる .zip ファイルによって定義されます。

* モジュールによって公開される R 関数を実装するファイル
* カスタム モジュールを説明する XML 定義ファイル

カスタム モジュールからアクセスできる機能を提供する追加の補助ファイルも .zip ファイルに含まれる場合があります。このオプションは、以下について説明します。

## クイック スタートの例
この例では、カスタム R モジュールに必要なファイルを作成し、それらのファイルを zip ファイルにパッケージして、Machine Learning ワークスペースにモジュールを登録する方法が示されています。

2 つのデータセット (データ フレーム) から行 (監視) を連結するために使用される Add Rows モジュールの標準実装を変更するカスタム My Add Rows モジュールの例について考慮します。Add Rows モジュールは、rbind アルゴリズムを使用して、最初の入力データセットの最後に、2 番目の入力データセットの行を追加します。同様に、カスタマイズされた `myAddRows` 関数も 2 つのデータセットを受け入れますが、入力として追加のブール型スワップ パラメーターも受け取ります。スワップ パラメーターが **FALSE** である場合、標準の実装と同じデータ セットを返します。しかし、スワップ パラメーターが **TRUE** である場合は、代わりに 2 つ目のデータセットの最後に最初の入力データセットの行を追加します。My Add Rows モジュールによって公開される R myAddRows 関数を実装するファイルには、以下の R コードが含まれます。

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	return (dataset)
	}

この `myAddRows` 関数を Azure Machine Learning モジュールとして公開するには、XML 定義ファイルを作成して My Add Rows モジュールの表示方法および動作方法を指定する必要があります。 

	<Module Version="v0.00.1" type="Public" insync="false" Owner="myName">
	  <GUID>{1CE529D1-B9D2-496F-AB42-8DBA60DE8279}</GUID>
	  <ID>myAddRows</ID>
	  <Name>My Add Rows</Name>	
	  <State>Custom</State>
	  <Description>This is my module description. </Description>
	  <Language Name="R" EntryPointFile="myAddRows.R" EntryPoint="myAddRows" />  
	    <Ports>
	      <output id="dataset" display="dataset" type="DataTable">
	        <Description>Combined Data</Description>
	      </output>
	      <input id="Dataset1" display="Dataset1" type="DataTable">
		    <Description>Input dataset 1</Description>
	      </input>
	      <input id="Dataset2" display="Dataset2" type="DataTable">
		    <Description>Input dataset 2</Description>
	      </input>
	    </Ports>
	    <Arguments>
	      <arg id="swap" display="swap" type="bool" >
	        <Description>Swaps inputs</Description>
	      </arg>
	    </Arguments>
	  <Category>My Category</Category>
	</Module>

 
XML ファイルで ID 要素の内容と関数名が正確に一致していることが重要であることに注意してください。 

これらの 2 つのファイルを *myAddRows.R* および *myAddRows.xml* として保存し、それらを *myAddRows.zip* ファイルと一緒に zip 圧縮します。

Machine Learning ワークスペースにそれらを登録するには、Machine Learning Studio のワークスペースに移動し、下部にある **[+新規]** ボタンをクリックし、**[モジュール] -> [zip パッケージから]** を選択して新規カスタム My Add Rows モジュールをアップロードします。

![](http://i.imgur.com/RFJhCls.png)

これで My Add Rows モジュールは Machine Learning の実験によってアクセスされる準備が整いました。

## XML 定義ファイルの要素

### 入力と出力
カスタム モジュールの入力と出力は、XML 定義ファイルのポート セクションの子要素で指定します。これらの入力と出力の要素の順序によって、ユーザーに表示されるレイアウト (UX) が決定します。XML ファイルのポート要素にリストされる最初の子は、  Machine Learning UX の一番左の入力ポートになります。入力と出力のポートをサポートするデータ型は、次のとおりです。 

**DataTable**:この型は、data.frame として R 関数に渡されます。実際、Machine Learning によってサポートされている型、および DataTable と互換性のある型 (たとえば、CSV ファイルまたは ARFF ファイル) は、  data.frame に自動的に変換されます。 

       <input id="dataset1" display="Input 1" type="DataTable" IsOptional="false">
           <Description>Input Dataset 1</Description>
       </input>

**Zip**: カスタム モジュールは、zip ファイルを入力として受け取ることができます。この入力は、関数の実行ディレクトリにアンパックします。

       <input id="zippedData" display="Zip Input" type="Zip" IsOptional="false">
           <Description>Zip Input for port</Description>
       </input>

これには、入力および出力の各ポートに関連付けられた ID 属性に一意の値が設定され、それらの値が R 関数で指定されたパラメーターに一致している必要があります。また、入力がオプションである属性に既定値を指定する必要があります。IsOptional 属性は、DataTable と Zip の両方の型のオプションであり、既定で false に設定されます。この既定値は、入力の型がオプションではないことを示します。


### パラメーター
カスタム モジュールのパラメーターは、XML 定義ファイルの引数セクションの子要素で指定します。ポート セクションの子要素と同様に、引数セクションでのパラメーターの順序によって UX 内で発生するレイアウトが定義されます。リストされている最初のパラメーターが最初の関数の最初のパラメーターとなります。パラメーターについて Machine Learning によってサポートされるタイプを以下にリストします。オプションの属性では、XML 定義で既定値を指定する必要があります。これらは、関数を使用するときにパラメーター値が指定されていない場合に、使用される値です。どの属性がオプションであるかを型ごとに示します。


**int** - 整数 (32 ビット) 型パラメーター。

       <arg id="intValue1" display="My int Param" type="int" IsOptional="false" MinValue="0" MaxValue="100">
           <Description>Integer Parameter 1</Description>
       </arg>

IsOptional、MinValue、および MaxValue は int のオプションです。

**double** - 倍精度浮動小数点型パラメーター。

       <arg id="doubleValue1" display="My double Param" type="double" IsOptional="false" min="0.000" max="0.999" default="0.3">
           <Description>Double Parameter 1</Description>
       </arg>
IsOptional、min、max、default は倍精度浮動小数点型のオプションです。

**bool** - UX のチェック ボックスで表されるブール型パラメーター。

       <arg id="boolValue1" display="My boolean Param" type="bool" default="true">
           <Description>Boolean Parameter 1 </Description>
       </arg>

default はブール型のオプションです。

**string** - 標準的な文字列。

        <arg id="stringValue1" display="My string Param" type="string" default="default value" IsOptional="true">
           <Description>String Parameter 1</Description>
        </arg>

default および IsOptional は文字列型のオプションです。

**ColumnPickerFor** - 列選択パラメーター。この型は、列の選択として UX に表示されます。列を選択する DataTable の ID は、型属性の値でテーブル部分を置き換えます。変数は、文字列のリストとして関数に渡されます。 

        <arg id="columnSelection1" display="My Column Param" type="ColumnPickerFor:table">
           <Description>My column selector Param 1</Description>		
        </arg>

たとえば、DataTable に dataset1 の ID が存在する場合、type は次のようになります。 

		type="ColumnPickerFor:dataset1" 
                            
**enum:<DropDown Type ID>**: 列挙型 (ドロップダウン) リスト。選択した値は、文字列として R 関数に渡されます。この型は、有効な列挙値が引数セクション内で最初に定義されている必要があります。

       <DropDownType id="myDropDown1">
           <o id="red" display="Red"/>
           <o id="yellow" display="Yellow"/>
           <o id="blue" display="Blue"/>
       </DropDownType>
       <arg id="enum1" display="My Enum Param" default="red" type="enum:myDropDown1">
           <Description>My Enum Param 1</Description>
       </arg>

入力および出力と同様、各パラメーターで一意の ID 値をそれらに関連付けることが重要になります。また、ID 値は R 関数で指定したパラメーターに対応している必要があります。このクイック スタートの例では、関連付けられている ID とパラメーターは *swap*です。

### 言語定義
XML 定義ファイルの言語要素は、言語固有の機能を定義します。R モジュールでは一般的に以下のように定義します。 

	<Language Name="R" EntryPointFile="myFunc.R" EntryPoint="myFunc" AddDisplayOutputPort="false"/>

これによって、特定の言語、関数が定義されているファイル、およびその定義内のエントリ ポイントが指定されます。AddDisplayOutputPort 属性は、言語要素ではオプションです。Execute R Script モジュール (リンク TBD) と同様、可視化プロット/グラフに使用する出力ポートを追加する場合、AddDisplayOutputPort タグに対して *true* を選択すると、追加の出力ポートが表示されます。 

### 補助機能

この例では公開されませんが、カスタム モジュールの作成者が使用できる多くの属性が存在します。たとえば、モジュールには確定的または非確定的動作が存在する場合があります。確定的モジュールでは、同じ入力データと同じパラメーターの構成が指定された場合、2 回目の実行が実行されることはありません。キャッシュされた結果が使用され、下流のすべてのモジュールに伝達されます。Azure Machine Learning における確定的モジュールの例は、Add Rows モジュールです。非確定的モジュールの例は Reader です。カスタム モジュールを非確定的にするには、以下の属性を定義に追加することによって既定の設定を変更します。

	<IsDeterministic>false</IsDeterministic>

### 補助ファイル

カスタム モジュールの ZIP ファイル内に配置されたすべてのファイルは、実行時に使用できるようなります。ディレクトリ構造がある場合は保持されます。つまり、ファイル ソーシングはローカルでの実行と Azure Machine Learning での実行で同じように機能します。 

たとえば、myAddRows に出力する前にデータセット内の NA を含む行と重複する行を削除するとし、ファイル removeDupNARows.R にそれらを実行する R 関数を既に記述済みであるとします。

	removeDupNARows <- function(dataFrame) {
		#Remove Duplicate Rows:
		dataFrame <- unique(dataFrame)
		#Remove Rows with NAs:
		finalDataFrame <- dataFrame[complete.cases(dataFrame),]
		return(finalDataFrame)
	}
以下のように myAddRows 関数で補助ファイル removeDupNARows.R を参照できます。

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
		source("removeDupNARows.R")
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	dataset <- removeDupNARows(dataset)
	return (dataset)
	}

'myAddRows.R'、'myAddRows.xml'、および 'removeDupNARows.R' をカスタム R モジュールとして含む zip ファイルをアップロードします。

### 複数の出力

サポート対象のデータ型の複数のオブジェクトを出力するために、適切な出力ポートを XML 定義ファイルで指定する必要があります。また、オブジェクトをリストとして返す必要があります。返されたリストに配置されたオブジェクトの順序を反映して、左から右に、出力オブジェクトが出力ポートに割り当てられます。
 
たとえば、dataset、Dataset1、および Dataset2 をそれぞれ出力ポート dataset、dataset1、および dataset2 に左から右に出力する場合、'myAddRows.xml' ファイルで出力ポートを次のように定義します。

	<Ports>
	    <output id="dataset" display="dataset" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
		<output id="Dataset1" display="dataset1" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
		<output id="Dataset2" display="dataset2" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
	    <input id="Dataset1" display="Dataset1" type="DataTable">
		    <Description>Input dataset 1</Description>
	    </input>
	    <input id="Dataset2" display="Dataset2" type="DataTable">
		    <Description>Input dataset 2</Description>
	    </input>
	</Ports>

オブジェクトのリストを 1 つのリストに正しい順序で 'myAddRows.R' に返します。

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	return (list(dataset, Dataset1, Dataset2))
	}

## 実行環境

TBD

## 次のステップ

TBD

<!--HONumber=49--> 
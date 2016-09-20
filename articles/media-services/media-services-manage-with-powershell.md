<properties 
	pageTitle="PowerShell を使用して Azure Media Services アカウントを管理する" 
	description="PowerShell コマンドレットを使用して Azure Media Services アカウントを管理する方法を説明します。" 
	authors="Juliako" 
	manager="erikre" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/04/2016"
	ms.author="juliako"/>


#PowerShell を使用して Azure Media Services アカウントを管理する


> [AZURE.SELECTOR]
- [ポータル](media-services-portal-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [REST ()](http://msdn.microsoft.com/library/azure/dn194267.aspx)


> [AZURE.NOTE] Azure Media Services アカウントを作成するには、Azure アカウントが必要です。アカウントがない場合は、無料試用アカウントを数分で作成することができます。詳細については、「<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure の無料試用版サイト</a>」をご覧ください。

##概要 

この記事では、Azure Resource Manager フレームワークにある Azure Media Services (AMS) 用の Azure PowerShell コマンドレットについて説明します。コマンドレットは、**Microsoft.Azure.Commands.Media** 名前空間にあります。

## バージョン

**ApiVersion**: "2015-10-01"
               

## New-AzureRmMediaService

メディア サービスを作成します。

### 構文

パラメーター セット: StorageAccountIdParamSet

	New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccountId] <string> [-Tags <hashtable>]  [<CommonParameters>]

パラメーター セット: StorageAccountsParamSet

	New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccounts] <PSStorageAccount[]> [-Tags <hashtable>]  [<CommonParameters>]

### パラメーター

**-ResourceGroupName &lt;String&gt;**

このメディア サービスが属するリソース グループの名前を指定します。

別名 | なし
---|---
必須 | true
位置 | 0
既定値 |なし
パイプライン入力を許可する |true(ByPropertyName)
ワイルドカード文字を許可する |false

**-AccountName &lt;String&gt;**

メディア サービスの名前を指定します。

別名 |Name
---|---
必須 |true
位置 |1
既定値 |なし
パイプライン入力を許可する |false
ワイルドカード文字を許可する |false

**-Location &lt;String&gt;**

メディア サービスのリソースの場所を指定します。

別名 |なし
---|---
必須 |true
位置 |2
既定値 |なし
パイプライン入力を許可する |true(ByPropertyName)
ワイルドカード文字を許可する |false

**-StorageAccountId &lt;String&gt;**

メディア サービスに関連付けられているプライマリ ストレージ アカウントを指定します。

- (Resource Manager API で作成された) 新しいストレージ アカウントのみがサポートされます。

- このストレージ アカウントは、存在し、メディア サービスと同じ場所を持っている必要があります。

別名 |なし
---|---
必須 |true
位置 |3
既定値 |なし
パイプライン入力を許可する |true(ByPropertyName)
パラメーター セット名 |StorageAccountIdParamSet
ワイルドカード文字を許可する|false

**-StorageAccounts &lt;PSStorageAccount[]&gt;**

メディア サービスに関連付けられたストレージ アカウントを指定します。

- (Resource Manager API で作成された) 新しいストレージ アカウントのみがサポートされます。

- このストレージ アカウントは、存在し、メディア サービスと同じ場所を持っている必要があります。

- プライマリとして指定できるストレージ アカウントは 1 つだけです。

別名 |なし
---|---
必須 |true
位置 |3
既定値 |なし
パイプライン入力を許可する |true(ByPropertyName)
パラメーター セット名 |StorageAccountsParamSet
ワイルドカード文字を許可する |false

**-Tags &lt;Hashtable&gt;**

メディア サービスに関連付けられているタグのハッシュ テーブルを指定します。

- 例: @{"tag1"="value1";"tag2"=:value2"}

別名 |なし
---|---
必須 |false
位置 |named
既定値 |なし
パイプライン入力を許可する |false
ワイルドカード文字を許可する |false

**&lt;CommandParameters&gt;**

このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。

### 入力

入力の型は、コマンドレットにパイプできるオブジェクトの型です。

### 出力  


出力の型は、コマンドレットが出力するオブジェクトの型です。

## Set-AzureRmMediaService

メディア サービスを更新します。

### 構文

	Set-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Tags <hashtable>] [-StorageAccounts <PSStorageAccount[]>]  [<CommonParameters>]

### パラメーター

**-ResourceGroupName &lt;String&gt;**

このメディア サービスが属するリソース グループの名前を指定します。

別名 |なし
---|---
必須 |true
位置 |0
既定値 |なし
パイプライン入力を許可する |true(ByPropertyName)
ワイルドカード文字を許可する |false

**-AccountName &lt;String&gt;**

メディア サービスの名前を指定します。

別名 |Name
---|---
必須 |True
位置 |1
既定値 |なし
パイプライン入力を許可する |true(ByPropertyName)
ワイルドカード文字を許可する |False

**-StorageAccounts &lt;PSStorageAccount[]&gt;**

メディア サービスに関連付けられたストレージ アカウントを指定します。

- (Resource Manager API で作成された) 新しいストレージ アカウントのみがサポートされます。

- このストレージ アカウントは、存在し、メディア サービスと同じ場所を持っている必要があります。

- プライマリとして指定できるストレージ アカウントは 1 つだけです。

別名 |なし
---|---
必須 |false
位置 |named
既定値 |なし
パイプライン入力を許可する |true(ByPropertyName)
パラメーター セット名 |StorageAccountsParamSet
ワイルドカード文字を許可する |false

**-Tags &lt;Hashtable&gt;**

このメディア サービスに関連付けられているタグのハッシュ テーブルを指定します。

- メディア サービスに関連付けられているタグは、お客様が指定した値に置き換えられます。

別名 |なし
---|---
必須 |False
位置 |named
既定値 |なし
パイプライン入力を許可する |true(ByPropertyName)
ワイルドカード文字を許可する |false

**&lt;CommandParameters&gt;**

このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。

### 入力

入力の型は、コマンドレットにパイプできるオブジェクトの型です。

### 出力  


出力の型は、コマンドレットが出力するオブジェクトの型です。

## Remove-AzureRmMediaService

メディア サービスを削除します。

### 構文

	Remove-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### パラメーター

**-ResourceGroupName &lt;String&gt;**

このメディア サービスが属するリソース グループの名前を指定します。

別名 |なし
---|---
必須 |true
位置 |0
既定値 |なし
パイプライン入力を許可する |true(ByPropertyName)
ワイルドカード文字を許可する |false

**-AccountName &lt;String&gt;**

メディア サービスの名前を指定します。

別名 |なし
---|---
必須 |true
位置 |2
既定値 |なし
パイプライン入力を許可する |true(ByPropertyName)
ワイルドカード文字を許可する |False

**&lt;CommandParameters&gt;**

このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。

### 入力

入力の型は、コマンドレットにパイプできるオブジェクトの型です。

### 出力  


出力の型は、コマンドレットが出力するオブジェクトの型です。

## Get-AzureRmMediaService

リソース グループ内のすべてのメディア サービス、または指定された名前を持つメディア サービスを取得します。

### 構文

ParameterSet: ResourceGroupParameterSet

	Get-AzureRmMediaService [-ResourceGroupName] <string>  [<CommonParameters>]	

ParameterSet: AccountNameParameterSet

	Get-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### パラメーター

**-ResourceGroupName &lt;String&gt;**

このメディア サービスが属するリソース グループの名前を指定します。

別名 |なし
---|---
必須 |true
位置 |0
既定値 |なし
パイプライン入力を許可する |true(ByPropertyName)
パラメーター セット名 |ResourceGroupParameterSet、AccountNameParameterSet
ワイルドカード文字を許可する false

**-AccountName &lt;String&gt;**

メディア サービスの名前を指定します。

別名 |なし
---|---
必須 |true
位置 |1
既定値 |なし
パイプライン入力を許可する |true(ByPropertyName)
パラメーター セット名 |AccountNameParameterSet
ワイルドカード文字を許可する |false

**&lt;CommandParameters&gt;**

このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。

### 入力

入力の型は、コマンドレットにパイプできるオブジェクトの型です。

### 出力  


出力の型は、コマンドレットが出力するオブジェクトの型です。

## Get-AzureRmMediaServiceKeys

メディア サービスのキーを取得します。

### 構文

	Get-AzureRmMediaServiceKeys [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### パラメーター

**-ResourceGroupName &lt;String&gt;**

このメディア サービスが属するリソース グループの名前を指定します。

別名 |なし
---|---
必須 |true
位置 |0
既定値 |なし
パイプライン入力を許可する |true(ByPropertyName)
ワイルドカード文字を許可する |false

**-AccountName &lt;String&gt;**

メディア サービスの名前を指定します。

別名 |なし
---|---
必須 |true
位置 |1
既定値 |なし
パイプライン入力を許可する |true(ByPropertyName)
ワイルドカード文字を許可する |false

**&lt;CommandParameters&gt;**

このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。

### 入力

入力の型は、コマンドレットにパイプできるオブジェクトの型です。

### 出力  


出力の型は、コマンドレットが出力するオブジェクトの型です。

## Set-AzureRmMediaServiceKey

メディア サービスのプライマリまたはセカンダリ キーを再生成します。

### 構文

	Set-AzureRmMediaServiceKey [-ResourceGroupName] <string> [-AccountName] <string> [-KeyType] <KeyType> {Primary | Secondary}  [<CommonParameters>]

### パラメーター

**-ResourceGroupName &lt;String&gt;**

このメディア サービスが属するリソース グループの名前を指定します。

別名 |なし
---|---
必須 |true
位置 |0
既定値 |なし
パイプライン入力を許可する |true(ByPropertyName)
ワイルドカード文字を許可する |false

**-AccountName &lt;String&gt;**

メディア サービスの名前を指定します。

別名 |なし
---|---
必須 |true
位置 |1
既定値 |なし
パイプライン入力を許可する |true(ByPropertyName)
ワイルドカード文字を許可する |false

**-KeyType &lt;KeyType&gt;**

メディア サービスのキーの種類を指定します。

- Primary または Secondary

別名 |なし
---|---
必須 |true
位置 |2
既定値 |なし
パイプライン入力を許可する |false
ワイルドカード文字を許可する |false

**&lt;CommandParameters&gt;**

このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。

### 入力

入力の型は、コマンドレットにパイプできるオブジェクトの型です。

### 出力  


出力の型は、コマンドレットが出力するオブジェクトの型です。

## Sync-AzureRmMediaServiceStorageKeys

メディア サービスに関連付けられたストレージ アカウントのストレージ アカウント キーを同期します。

### 構文

	Sync-AzureRmMediaServiceStorageKeys [-ResourceGroupName] <string> [-MediaServiceAccountName] <string>    [-StorageAccountName] <string>  [<CommonParameters>]

### パラメーター

**-ResourceGroupName &lt;String&gt;**

このメディア サービスが属するリソース グループの名前を指定します。

別名 |なし
---|---
必須 |true
位置 |0
既定値 |なし
パイプライン入力を許可する |true(ByPropertyName)
ワイルドカード文字を許可する |false

**-AccountName &lt;String&gt;**

メディア サービスの名前を指定します。

別名 |なし
---|---
必須 |true
位置 |1
既定値 |なし
パイプライン入力を許可する |true(ByPropertyName)
ワイルドカード文字を許可する |false

**-StorageAccountId &lt;String&gt;**

メディア サービスに関連付けられたストレージ アカウントを指定します。

別名 |ID
---|---
必須 |true
位置 |2
既定値 |なし
パイプライン入力を許可する | true(ByPropertyName)
ワイルドカード文字を許可する |false

**&lt;CommandParameters&gt;**

このコマンドレットは、一般的なパラメーターをサポートしています。-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction、-WarningVariable です。

### 入力

入力の型は、コマンドレットにパイプできるオブジェクトの型です。

### 出力  


出力の型は、コマンドレットが出力するオブジェクトの型です。

## 次のステップ 

Media Services のラーニング パスをご覧ください。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 

<!---HONumber=AcomDC_0907_2016-->
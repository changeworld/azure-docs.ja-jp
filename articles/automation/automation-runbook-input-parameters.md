<properties 
   pageTitle="Runbook の入力パラメーター"
   description="Runbook の入力パラメーターを利用すれば、開始時に Runbook にデータを渡すことができて Runbook の柔軟性が上がります。この記事では、入力パラメーターを Runbook で使用するさまざまなシナリオについて説明します。"
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/16/2015"
   ms.author="sngun"/>

# Runbook の入力パラメーター

Runbook の入力パラメーターを利用すれば、開始時に Runbook にデータを渡すことができて Runbook の柔軟性が上がります。パラメーターにより、Runbook アクションの対象を特定のシナリオや環境に設定できます。この記事では、入力パラメーターを Runbook で使用するさまざまなシナリオを紹介します。

## 入力パラメーターを構成する

入力パラメーターは PowerShell、PowerShell Workflow、Graphical Runbook で構成できます。Runbook にはデータ型の異なる複数のパラメーターを指定することも、パラメーターをまったく指定しないこともできます。入力パラメーターには必須と任意があります。任意のパラメーターには既定値を割り当てることができます。Runbook に構成された入力パラメーターには、利用可能な何らかのメソッドで Runbook を起動するとき、値を割り当てることができます。たとえば、UI、Webhook、Web サービスを介して Runbook を起動したり、別の Runbook のインラインで呼び出される子 Runbook として Runbook を起動したりする場合などです。

## PowerShell と PowerShell Workflow の Runbook で入力パラメーターを構成する

Azure Automation の PowerShell Runbook と [PowerShell ワークフロー Runbook](automation-first-runbook-textual.md) では、次の属性で定義されている入力パラメーターを利用できます。

| **プロパティ** | **説明** |
|:--- |:---|
| 型 | 必須。パラメーター値に必要なデータ型。.Net 型はすべて有効です。 |
| 名前 | 必須。パラメーターの名前。これは Runbook 内で一意にする必要があります。文字、数字、アンダースコアのみで作成し、先頭は文字にする必要があります。 |
| 必須 | 省略可能。パラメーターの値を指定する必要があるかどうかを示します。これを **$true** に設定した場合、Runbook の起動時に値を指定する必要があります。これを **$false** に設定した場合、値は任意になります。 |
| 既定値 | 省略可能。Runbook の起動時に値が渡されない場合にパラメーターに使用する値を指定します。既定値はすべてのパラメーターに設定可能であり、必須設定に関係なく、自動的にパラメーターを任意にします。 |

Windows PowerShell では、検証、エイリアス、パラメーター セットなど、ここに記載されているものより多くの入力パラメーター属性に対応していますが、Azure Automation では、現在のところ、上記の属性のみご利用いただけます。

PowerShell ワークフロー Runbook のパラメーター定義には次の一般形式があります。複数のパラメーターはコンマで分けられます。

```
     Param
     (
         [Parameter (Mandatory= $true/$false)]
         [Type] Name1 = <Default value>,

         [Parameter (Mandatory= $true/$false)]
         [Type] Name2 = <Default value>
     ) 
```

>[AZURE.NOTE] パラメーターを定義するとき、**Mandatory** 属性を指定しない場合、既定でパラメーターは任意と見なされます。また、PowerShell ワークフロー Runbook のパラメーターに既定値を設定した場合、**Mandatory** 属性値に関係なく、PowerShell は任意のパラメーターとして扱います。

例として、PowerShell ワークフロー Runbook に仮想マシン (1 台の VM またはサービス内の全 VM) の詳細を出力する入力パラメーターを構成します。下のスクリーンショットでわかるように、この Runbook には 2 つのパラメーターがあります。仮想マシンの名前とサービスの名前です。

![Automation PowerShell ワークフロー](media/automation-runbook-input-parameters/automation_01_PowerShellWorkflow.png)

このパラメーター定義では、**$VMName** と **$ServiceName** は文字列型の単純なパラメーターですが、PowerShell と PowerShell ワークフローの Runbook では、入力パラメーターとしてあらゆる単純な型と **object** や **PSCredential** のような複雑な型に対応しています。

Runbook に object 型の入力パラメーターが含まれている場合、値を渡すために、(name,value) の組み合わせで PowerShell ハッシュテーブルを使用します。たとえば、Runbook に次のパラメーターがある場合、

     [Parameter (Mandatory = $true)]
     [object] $FullName

パラメーターに次の値を渡すことができます。

    @{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}


## グラフィカル Runbook の入力パラメーターを構成する

入力パラメーターでグラフィカル Runbook を構成するために、仮想マシン (1 台の VM またはサービス内の全 VM) の詳細を出力する[グラフィカル Runbook](automation-first-runbook-graphical.md) を構成します。この Runbook は、2 つの主要なアクティビティで構成されます。

* Azure で認証する [**Add-AzureAccount**](https://msdn.microsoft.com/library/dn495128.aspx)。
* すべての仮想マシンを取得する [**Get-AzureVM**](https://msdn.microsoft.com/library/azure/dn495236.aspx)。

[**Write-Output**](https://technet.microsoft.com/library/hh849921.aspx) アクティビティを利用し、仮想マシンの名前を出力できます。**Get-AzureVM** アクティビティは 2 つのパラメーターを受け取ります。**仮想マシンの名前**と**サービス アカウントの名前**です。これらのパラメーターは Runbook を起動するたびに異なる値を必要とする可能性があるため、入力パラメーターを Runbook に追加できます。以下は入力パラメーターを追加する手順です。

1. **[Runbook]** ブレードからグラフィカル Runtbook を選択し、[編集](automation-graphical-authoring-intro.md)します。 

2. **[編集]** ブレードで **[入力と出力]** をクリックし、**[入力と出力]** ブレードを開きます。

    ![Automation グラフィカル Runbook](media/automation-runbook-input-parameters/automation_02_GraphicalRunbook.png)

 
3. **[入力と出力]** ブレードには、Runbook に定義されている入力パラメーターの一覧が表示されます。このブレードから、新しい入力パラメーターを追加したり、既存の入力パラメーターの構成を編集したりできます。Runbook に新しいパラメーターを追加するには、**[入力の追加]** をクリックし、[Runbook 入力パラメーター] ブレードを開きます。このブレードで次のパラメーターを構成できます。

    | **プロパティ** | **説明** |
    |:--- |:---|
    | 名前 | 必須。パラメーターの名前。これは Runbook 内で一意にする必要があります。文字、数字、アンダースコアのみで作成し、先頭は文字にする必要があります。 |
    | 説明 | 省略可能。入力パラメーターの目的に関する説明。 |
    | 型 | 省略可能。パラメーター値に必要なデータ型。サポートされているパラメーター型は String、Int32、Int64、Decimal、Boolean、DateTime、Object です。データ型が選択されていない場合、既定値は String になります。 |
    | 必須 | 省略可能。パラメーターの値を指定する必要があるかどうかを示します。**[はい]** を選択した場合、Runbook の起動時に値を指定する必要があります。**[いいえ]** を選択した場合、Runbook の起動時に値は必要ありません。既定値を設定できます。 |
    | 既定値 | 省略可能。Runbook の起動時に値が渡されない場合にパラメーターに使用する値を指定します。必須ではないパラメーターに既定値を設定できます。**[カスタム]** を選択し、既定値を設定します。Runbook の起動時に別の値を指定しない限り、この値が使用されます。既定値を指定しない場合、**[なし]** を選択します。 |  

    ![AddNewInput](media/automation-runbook-input-parameters/automation_03_AddNewInput.png)

4. **Get-AzureVM** アクティビティで使用される 2 つのパラメーターを次のプロパティで作成します。

    * **Parameter1:** 
    名前 – VMName、
    型 – String、
    必須 – いいえ
	
    * **Parameter2:** 
    名前 – ServiceName、
    型 – String、
    必須 – いいえ、
    既定値 – カスタム、
    カスタム既定値 – \<仮想マシンを含む既定サービスの名前>

5. パラメーターを追加したら、**[OK]** をクリックします。パラメーターが **[入力と出力]** ブレードに表示されます。**[OK]** をもう一度クリックし、Runbook を**保存**し、**公開**します。

## Runbook の入力パラメーターに値を割り当てる

次のシナリオでは、Runbook の入力パラメーターに値を割り当てることができます。

### Runbook を起動し、パラメーターを割り当てる

Azure ポータル UI、Webhook、PowerShell コマンドレット、REST API、SDK など、Runbook はさまざまな方法で起動できます。以下では、Runbook を起動し、パラメーターを割り当てるためのさまざまな方法について説明します。

* **Azure ポータルを介して公開済み Runbook を起動し、パラメーターを割り当てる**

[Runbook を起動する](automation-starting-a-runbook#starting-a-runbook-with-the-azure-portal.md)と、**[Runbook の開始]** ブレードが開きます。ここで、作成したパラメーターの値を構成できます。

![ポータルによる起動](media/automation-runbook-input-parameters/automation_04_StartRunbookUsingPortal.png)

入力テキストボックスの下にあるラベルにパラメーターの属性セットを確認できます。必須または任意、型、既定値です。パラメーター名の横にあるヘルプ バルーンでは、パラメーター入力値を決定するために必要なすべての主要情報を確認できます。パラメーターの必須性、型、既定値、その他の役に立つ注記などです。

![ヘルプ バルーン](media/automation-runbook-input-parameters/automation_05_HelpBaloon.png)


>[AZURE.NOTE] 文字列型のパラメーターの場合、文字列値に**空**を指定できます。入力パラメーター テキストボックスに **[EmptyString]** を入力すると、空の文字列がパラメーターに渡されます。また、文字列型パラメーターでは **Null** 値を渡すことができません。文字列パラメーターに何の値も渡さないと、PowerShell はそれを Null として解釈します。

* **PowerShell コマンドレットを利用して公開済み Runbook を起動し、パラメーターを割り当てる**

    * **Azure サービス管理コマンドレット:** [Start-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690259.aspx) を利用し、既定のリソース グループで作成された Automation Runbook を起動できます。

    **例:**

      ```
        $params = @{“VMName”=”WSVMClassic”; ”ServiceName”=”WSVMClassicSG”}

        Start-AzureAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” -Parameters $params
      ```


    * **Azure リソース マネージャー コマンドレット:** [Start-AzureRMAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) を利用し、リソース グループで作成された Automation Runbook を起動できます。

    **例:**

      ```
        $params = @{“VMName”=”WSVMClassic”;”ServiceName”=”WSVMClassicSG”}

        Start-AzureRMAutomationRunbook -AutomationAccountName “TestAutomationRG” -Name “Get-AzureVMGraphical” –ResourceGroupName “RG1” -Parameters $params
      ```

>[AZURE.NOTE] 渡した入力パラメーターと共に、PowerShell コマンドレットで Runbook を起動すると、値 **PowerShell** で既定のパラメーター **MicrosoftApplicationManagementStartedBy** が作成されます。このパラメーターは [ジョブの詳細] ブレードに表示されます。

* **SDK で Runbook を起動し、パラメーターを割り当てる**

    * **Azure サービス管理メソッド:** プログラミング言語の SDK を利用して Runbook を起動できます。以下は、Automation アカウントで Runbook を起動する C# コード スニペットです。完全なコードは [GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)にあります。  

    ```      
        public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
        {
            var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
            {
                Properties = new JobCreateProperties 
                {
                    Runbook = new RunbookAssociationProperty
                    {
                        Name = runbookName
                    },
                        Parameters = parameters
                }
            });
            return response.Job;
        }
    ```
      
    * **Azure リソース マネージャー メソッド:** プログラミング言語の SDK を利用して Runbook を起動できます。以下は、Automation アカウントで Runbook を起動する C# コード スニペットです。完全なコードは [GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)にあります。  

    ```
        public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
        {
           var response = AutomationClient.Jobs.Create(resourceGroup, automationAccount, new JobCreateParameters
           {
               Properties = new JobCreateProperties 
               {
                   Runbook = new RunbookAssociationProperty
                   {
                       Name = runbookName
                   },
                       Parameters = parameters
               }
           });
        return response.Job;
        }
    ```

このメソッドを開始するには、Runbook パラメーター、VMName と ServiceName、それらの値を保存するディクショナリを作成し、Runbook を起動します。以下は、上記で定義したメソッドを呼び出す C# コード スニペットです。

```
    IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();

    // Add parameters to the dictionary.
    RunbookParameters.Add("VMName", "WSVMClassic");
    RunbookParameters.Add("ServiceName", "WSVMClassicSG");

    //Call the StartRunbook method with parameters
    StartRunbook(“Get-AzureVMGraphical”, RunbookParameters);
```

* **REST API で Runbook を起動し、パラメーターを割り当てる**

**PUT** メソッドと次の要求 URI を利用し、Runbook ジョブを作成し、Azure Automation REST API で起動できます。

    https://management.core.windows.net/<subscription-id>/cloudServices/<cloud-service-name>/resources/automation/~/automationAccounts/<automation-account-name>/jobs/<job-id>?api-version=2014-12-08

要求 URI で、次のパラメーターを置き換えます。
 
* **subscription-id:** Azure のサブスクリプション ID。  
* **cloud-service-name:** 要求の送信先とするクラウド サービスの名前。  
* **automation-account-name:** 指定したクラウド サービス内でホストされている Automation アカウントの名前。  
* **job-id:** ジョブの GUID。PowerShell の GUID は **[GUID]::NewGuid().ToString()** コマンドレットで作成できます。
	
Runbook ジョブにパラメーターを渡すには、要求本体を使用します。JSON 形式で指定された 2 つのプロパティを受け取ります。

* **Runbook Name** – 必須。開始するジョブの Runbook の名前。  
* **Runbook Parameters** – 任意。(名前、値) 形式のパラメーターリストのディクショナリ。名前は文字列型にする必要があります。値には有効なあらゆる JSON 値を指定できます。 

VMName と ServiceName をパラメーターとして先に作成した **Get-AzureVMTextual** Runbook を起動するには、要求本文に次の JSON 形式を使用します。

```
        {
           "properties":{
           "runbook":{
               "name":"Get-AzureVMTextual"
           },
           "parameters":{
               "VMName":"WSVMClassic",
               "ServiceName":”WSCS1”
           }
          }
       }
```

ジョブが作成されると、HTTP ステータス コード 201 が返されます。応答ヘッダーと応答本文に関する詳細については、「[REST API を利用して Runbook を作成する](https://msdn.microsoft.com/library/azure/mt163849.aspx)」を参照してください。

### Runbook をテストし、パラメーターを割り当てる

テスト オプションを利用し、Runbook の草案バージョンを[テスト](automation-testing-runbook.md)すると、**[テスト]** ブレードが開きます。このブレードで、先に作成したパラメーターの値を構成できます。

![テストし、パラメーターを割り当てる](media/automation-runbook-input-parameters/automation_06_TestAndAssignParameters.png)

### スケジュールを Runbook にリンクし、パラメーターを割り当てる

Runbook に[スケジュールをリンク](automation-scheduling-a-runbook.md)し、特定の時刻に開始できます。スケジュールを作成すると、入力パラメーターが割り当てられます。スケジュールで Runbook が起動すると、それらの値が使用されます。必須のパラメーター値をすべて指定しないと、スケジュールは保存できません。

![スケジュールし、パラメーターを割り当てる](media/automation-runbook-input-parameters/automation_07_ScheduleAndAssignParameters.png)

### Runbook の Webhook を作成し、パラメーターを割り当てる

Runbook の [Webhook](automation-webhooks.md) を作成し、Runbook 入力パラメーターを構成できます。必須のパラメーター値をすべて指定しないと、Webhook は保存できません。

![Webhook を作成し、パラメーターを割り当てる](media/automation-runbook-input-parameters/automation_08_CreateWebhookAndAssignParameters.png)

Webhook を利用して Runbook を実行すると、定義した入力パラメーターと共に **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** が送信されます。クリックして WebhookData パラメーターを展開と、詳細が表示されます。

![WebhookData パラメーター](media/automation-runbook-input-parameters/automation_09_WebhookDataParameter.png)


## 次のステップ

- Runbook の入力と出力の詳細については、「[Azure Automation: Runbook 入力、出力、入れ子 Runbook](https://azure.microsoft.com/blog/azure-automation-runbook-input-output-and-nested-runbooks/)」を参照してください。
- Runbook を起動するさまざまな方法については、「[Runbook の起動](automation-starting-a-runbook.md)」を参照してください。
- テキスト Runbook を編集する方法については、「[テキスト Runbook の編集](automation-edit-textual-runbook.md)」を参照してください。
- グラフィカル Runbook を編集する方法については、「[Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)」を参照してください。

<!---HONumber=AcomDC_1223_2015-->

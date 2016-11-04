---
title: Azure Batch アカウントの作成 | Microsoft Docs
description: Azure ポータルで、クラウド内で大規模な並列ワークロードを実行する Azure Batch アカウントを作成する方法について説明します
services: batch
documentationcenter: ''
author: mmacy
manager: timlt
editor: ''

ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/21/2016
ms.author: marsma

---
# Azure Portal を使用して Azure Batch アカウントを作成する
> [!div class="op_single_selector"]
> * [Azure ポータル](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
> 
> 

[Azure Portal][azure_portal] で Azure Batch アカウントを作成する方法と、アクセス キーやアカウント URL のような重要なアカウント プロパティを確認できる場所について説明します。また、Batch の価格設定や、Azure Storage アカウントを Batch アカウントにリンクして[アプリケーション パッケージ](batch-application-packages.md)の使用と[ジョブおよびタスク出力の保持](batch-task-output.md)を可能にする方法についても紹介します。

## Batch アカウントを作成する
1. [Azure Portal][azure_portal] にサインインします。
2. **[新規]**、**[Compute]**、**[Batch Service]** の順にクリックします。
   
    ![Marketplace での Batch][marketplace_portal]
3. **[新しい Batch アカウント]** ブレードが表示されます。ブレードの各要素の説明については、下記の *a* ～ *e* の項目を参照してください。
   
    ![Batch アカウントを作成する][account_portal]
   
    a.**アカウント名**: Batch アカウントの一意の名前。この名前は、アカウントが作成されている Azure リージョン内で一意である必要があります (下の「*場所*」を参照してください)。アカウント名に含めることができるのは、英小文字と数字のみで、文字数は 3 ～ 24 文字にする必要があります。
   
    b.**サブスクリプション**: Batch アカウントを作成するサブスクリプション。サブスクリプションが 1 つのみの場合は、既定でそのサブスクリプションが選択されます。
   
    c.**リソース グループ**: 新しい Batch アカウント用の既存のリソース グループ。必要に応じて、新しく作成することもできます。
   
    d.**場所**: Batch アカウントを作成する Azure リージョン。サブスクリプションとリソース グループでサポートされているリージョンのみがオプションとして表示されます。
   
    e.**ストレージ アカウント** (省略可能): 新しい Batch アカウントに関連付ける (リンクする) **汎用**のストレージ アカウント。詳細については、下の「[リンクされた Azure Storage アカウント](#linked-azure-storage-account)」を参照してください。
4. **[作成]** をクリックしてアカウントを作成します。
   
   アカウントを**デプロイ中**であることがポータルに示され、完了すると *[通知]* に **"デプロイメントが成功しました"** という通知が表示されます。

## Batch アカウントのプロパティを表示する
アカウントが作成されたら、**[Batch アカウント]** ブレードを開いて Batch アカウントの設定とプロパティにアクセスできます。[Batch アカウント] ブレードの左側のメニューから、アカウントのすべての設定とプロパティにアクセスできます。

![Batch account blade in Azure portal][account_blade]

* **Batch アカウント URL**: [Batch 開発 API](batch-technical-overview.md#batch-development-apis) で作成するアプリケーションには、リソースを管理し、アカウント内のジョブを実行するためにアカウント URL が必要です。Batch アカウント URL の形式を次に示します。
  
    `https://<account_name>.<region>.batch.azure.com`

![Batch account URL in portal][account_url]

* **アクセス キー**: アプリケーションには、Batch アカウント内のリソースを操作する際に使用するアクセス キーも必要です。Batch アカウントのアクセス キーを表示または再生成するには、[Batch アカウント] ブレードの左側メニューにある **[検索]** ボックスに「`keys`」と入力し、**[キー]** を選択します。
  
    ![Batch account keys in Azure portal][account_keys]

## 価格
Batch アカウントは "Free レベル" のみで提供されます。つまり、Batch アカウント自体に課金されることはありません。課金の対象となるのは、基になる Azure コンピューティング リソースのうち Batch ソリューションが使用する部分と、ワークロードの実行時に他のサービスが使用するリソースです。たとえば、プール内のコンピューティング ノードや、タスクの入力または出力として Azure Storage に格納するデータに対して課金されます。同様に、Batch の[アプリケーション パッケージ](batch-application-packages.md)機能を使用している場合は、アプリケーション パッケージを格納するために使用する Azure Storage リソースが課金の対象となります。詳細については、「[Batch の価格][batch_pricing]」を参照してください。

## リンクされた Azure Storage アカウント
既に述べたように、(必要に応じて) **汎用**の Storage アカウントを Batch アカウントにリンクすることができます。Batch の[アプリケーション パッケージ](batch-application-packages.md)機能では、[Batch File Conventions .NET](batch-task-output.md) ライブラリの場合と同様に、リンクされた汎用の Storage アカウント内の BLOB ストレージを使用します。これらのオプション機能は、Batch タスクで実行するアプリケーションのデプロイや、そのアプリケーションによって生成されるデータの保持に役立ちます。

「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」の手順 5.「[ストレージ アカウントの作成](../storage/storage-create-storage-account.md#create-a-storage-account)」で説明されているように、Batch では、現時点で**汎用**のストレージ アカウントの種類 "*のみ*" がサポートされています。Azure Storage アカウントを Batch アカウントにリンクする場合は、必ず**汎用**のストレージ アカウント "*のみ*" をリンクしてください。

![Creating a "General purpose" storage account][storage_account]

Batch アカウント専用として使用する Storage アカウントを作成することをお勧めします。

> [!WARNING]
> リンクされた Storage アカウントのアクセス キーを再生成する際は次の点に注意してください。Storage アカウントのキーは 1 つだけ再生成し、リンクされた Storage アカウントのブレードにある **[キーの同期]** をクリックします。キーがプール内のコンピューティング ノードに反映されるまで 5 分待ってから、必要に応じて他のキーの再生成と同期を行います。両方のキーを同時に再生成すると、コンピューティング ノードはどちらのキーも同期できず、Storage アカウントにアクセスできなくなります。
> 
> 

  ![Regenerating storage account keys][4]

## Batch サービスのクォータと制限
Azure サブスクリプションやその他の Azure サービスと同様に、Batch アカウントにも特定の[クォータと制限](batch-quota-limit.md)が適用される点に注意してください。Batch アカウントの現在のクォータは、ポータル内のアカウントの **[プロパティ]** に表示されます。

![Batch account quotas in Azure portal][quotas]

Batch ワークロードの設計やスケールアップを行う際は、これらのクォータに留意してください。たとえば、プールのコンピューティング ノード数がターゲットとして指定した数に満たない場合は、Batch アカウントのコア クォータ制限に達している可能性があります。

また、Azure サブスクリプションで使用できる Batch アカウントの数は 1 つだけではないという点にも注意してください。1 つの Batch アカウントで複数の Batch ワークロードを実行することも、同じサブスクリプションでありながら異なる Azure リージョンの複数の Batch アカウントにワークロードを分散することもできます。

これらのクォータの多くは、Azure Portal で無料の製品サポート要求を送信するだけで増やすことができます。クォータ引き上げ要求の詳細については、「[Azure Batch サービスのクォータと制限](batch-quota-limit.md)」を参照してください。

## その他の Batch アカウント管理オプション
Azure Portal を利用する方法に加えて、次に示す方法でも Batch アカウントを作成および管理できます。

* [Batch PowerShell コマンドレット](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](../xplat-cli-install.md)
* [Batch Management .NET](batch-management-dotnet.md)

## 次のステップ
* Batch サービスの概念と機能の詳細については、[Azure Batch 機能の概要](batch-api-basics.md)に関するページを参照してください。この記事では、プール、コンピューティング ノード、ジョブ、タスクなど、主要な Batch リソースについて説明すると共に、大規模なコンピューティング ワークロードを実行する機能の概要について説明しています。
* [Batch .NET クライアント ライブラリ](batch-dotnet-get-started.md)を使用した Batch 対応アプリケーションの開発に関する基本事項を確認してください。この[入門記事](batch-dotnet-get-started.md)では、Batch サービスを使用して複数のコンピューティング ノードでワークロードを実行する実用アプリケーションの開発手順を説明しています。また、Azure Storage を使用してワークロード ファイルのステージングと取得を行う方法についても取り上げています。

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Regenerating storage account keys"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png

<!---HONumber=AcomDC_0921_2016-->
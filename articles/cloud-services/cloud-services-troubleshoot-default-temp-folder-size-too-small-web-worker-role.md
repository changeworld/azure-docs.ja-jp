<properties 
   pageTitle="ロールに対する既定の TEMP フォルダーのサイズが小さすぎる | Microsoft Azure"
   description="クラウド サービス ロールでは、TEMP フォルダー用の領域量が限られています。この記事では、領域不足の回避方法に関する推奨事項をいくつか示します。"
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="msmets"
   editor=""
   tags="top-support-issue"/>
<tags 
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/14/2015"
   ms.author="daleche" />

# クラウド サービスの Web/worker ロールに対する既定の一時フォルダーのサイズが小さすぎる

クラウド サービスの worker または Web ロールの既定の一時ディレクトリの最大サイズは 100 MB ですが、ある時点でいっぱいになる可能性があります。この記事では、一時ディレクトリの領域不足を回避する方法について説明します。

>[AZURE.NOTE]これは Web および worker ロールを使用する Azure SDK 1.0 から SDK 1.4 にのみ適用されます。

## Azure カスタマー サポートへの問い合わせ

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](http://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。

または、Azure サポート インシデントを送信できます。その場合は、[Azure サポートのサイト](http://azure.microsoft.com/support/options/)に移動して、**[サポートの要求]** をクリックします。Azure サポートの使用方法の詳細については、「[Azure サポートに関する FAQ](http://azure.microsoft.com/support/faq/)」を参照してください。


## 領域が不足する理由
標準的な Windows 環境変数 TEMP と TMP は、アプリケーションで実行されているコードで使用可能です。TEMP と TMP は両方とも、最大サイズが 100 MB の 1 つのディレクトリを指します。このディレクトリに格納されているデータは、ホステッド サービスのライフサイクル全体で保持されません。ホステッド サービスのロール インスタンスがリサイクルされると、ディレクトリはクリーニングされます。

## 問題の修正に関する推奨事項
次のいずれかの代替手段を実装します。

- ローカル ストレージ リソースを構成し、**TEMP** や **TMP** を使用せずに直接アクセスします。アプリケーション内で実行されているコードからローカル ストレージ リソースにアクセスするには、[RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) メソッドを呼び出します。ローカル ストレージ リソースの設定の詳細については、「[ローカル ストレージ リソースを構成する](cloud-services-configure-local-storage-resources.md)」を参照してください。

- ローカル ストレージ リソースを構成し、TEMP と TMP ディレクトリがローカル ストレージ リソースのパスを指すように指定します。この変更は [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) メソッド内で実行する必要があります。

次のコード例では、OnStart メソッド内から TEMP および TMP のターゲット ディレクトリを変更する方法を示します。


```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore" 
            //                  cleanOnRoleRecycle="false" 
            //                  sizeInMB="1024" />
            // </LocalResources>
            
            string customTempLocalResourcePath = 
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);
            
            // The rest of your startup code goes here…
            
            return base.OnStart();
        }
    }
}
```

## 次のステップ

クラウド サービスの他の[トラブルシューティングに関する記事](..\?tag=top-support-issue&service=cloud-services)を参照します。

<!---HONumber=Oct15_HO4-->
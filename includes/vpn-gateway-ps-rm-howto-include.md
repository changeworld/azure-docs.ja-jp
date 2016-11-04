モジュールのインストールには、PowerShell ギャラリーを使った方法と Web Platform Installer を使った方法があります。最終的な結果はほぼ同じですが、選択したインストール方法によって、モジュールのインストール先となるコンピューターの既定の場所が異なります。

PowerShell ギャラリーからインストールした場合、*%ProgramFiles%\\WindowsPowerShell\\Modules* がファイルの既定の格納場所になります。Web Platform Installer からインストールした場合は、*%ProgramFiles%\\Microsoft SDKs\\Azure\\PowerShell* がファイルの既定の格納場所になります。そのため、今後のコマンドレットの更新に伴うエラーを防ぐために、どちらかのインストール方法に統一することをお勧めします。Web Platform Installer では、月に 1 回、最新のコマンドレットが提供されます。ギャラリーの場合は、最新版のコマンドレットがそのリリース時に提供されます。そのため、ギャラリーの方が好ましいと考えるユーザーもいます。

Azure PowerShell のインストールの詳細については、「[Azure PowerShell のインストールおよび構成方法](../articles/powershell-install-configure.md)」を参照してください。

**PowerShell ギャラリーからモジュールをインストールするには**

1. ギャラリーから直接リソース マネージャー モジュールをインストールするには、Windows PowerShell を管理者として開き、次のように入力します。
   
        Install-Module AzureRM
        Install-AzureRM
2. インストールしたモジュールを使用するためには、それらをインポートする必要があります。
   
        Import-AzureRM

**Web Platform Installer を使用してモジュールをインストールするには**

* モジュールは、[Web Platform Installer](http://aka.ms/webpi-azps) を使用してインストールすることができます。リンクをクリックすると、インストーラーが起動します。
* Web Platform Installer の使用時にエラーが表示された場合は、以前のバージョンのコマンドレットを既にギャラリーを使用してインストール済みであることが原因である可能性があります。こちらの[ブログ記事](https://azure.microsoft.com/blog/azps-1-0/)を参照してください。以前のバージョンのモジュールを削除し、正常な状態に戻す方法が記載されています。通常、エラーは、インストール方法を (Web Platform Installer からギャラリーに、またはギャラリーから Web Platform Installer に) 切り替えたときに発生します。以前インストールしたモジュールを削除すると、この問題が解消され、新しい場所からインストールできるようになります。

<!---HONumber=AcomDC_0218_2016-->
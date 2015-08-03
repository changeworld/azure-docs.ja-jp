### Composer 経由でインストールする

1. [Git をインストールします][install-git]。 

 
	> [AZURE.NOTE]Windows では、Git 実行可能ファイルを PATH 環境変数に追加する必要があります。

2. プロジェクトのルートに **composer.json** という名前のファイルを作成して、次のコードを追加します。

        {
            "repositories": [
                {
                    "type": "pear",
                    "url": "http://pear.php.net"
                }
            ],
            "require": {
                "pear-pear.php.net/mail_mime" : "*",
                "pear-pear.php.net/http_request2" : "*",
                "pear-pear.php.net/mail_mimedecode" : "*",
                "microsoft/windowsazure": "*"
            }
        }


3. **[composer.phar][composer-phar]** をプロジェクトのルートにダウンロードします。

4. コマンド プロンプトを開き、次のコマンドをプロジェクトのルートで実行します。

		php composer.phar install

### 手動でインストールする

Azure 向け PHP クライアント ライブラリを手動でダウンロードしてインストールするには、次のステップに従います。

1. ライブラリが含まれている .zip アーカイブを [GitHub][php-sdk-github] からダウンロードします。または、リポジトリをフォークして、ローカル コンピューターにその複製を作成しますこの 2 つ目の方法では、GitHub アカウントが必要であるほか、Git がローカルにインストールされている必要があります。

	
	> [AZURE.NOTE]Azure 向け PHP クライアント ライブラリは、[HTTP_Request2](http://pear.php.net/package/HTTP_Request2)、[Mail_mime](http://pear.php.net/package/Mail_mime)、および [Mail_mimeDecode](http://pear.php.net/package/Mail_mimeDecode) の PEAR パッケージに依存しています。これらの依存関係を解決するには、[PEAR パッケージ マネージャー](http://pear.php.net/manual/en/installation.php)を使用してこれらのパッケージをインストールすることをお勧めします。


2. ダウンロードしたアーカイブの `WindowsAzure` ディレクトリをアプリケーションのディレクトリ構造にコピーします。

(PEAR パッケージとしてのインストールに関する情報を含めて) Azure 向け PHP クライアント ライブラリのインストールの詳細については、[PHP 用 Azure SDK のダウンロードに関するページ][download-SDK-PHP]を参照してください。


[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar

<!---HONumber=July15_HO4-->
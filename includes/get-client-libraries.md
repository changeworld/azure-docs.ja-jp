### Composer 経由でインストールする

1. [Git をインストールします][]。

    <div class="dev-callout"> 
	<b>注</b> 
	<p>Windows では、Git 実行可能ファイルを PATH 環境変数に追加する必要があります。</p>
	</div>

2. プロジェクトのルートに **composer.json** という名前のファイルを作成して、次のコードを追加します。

        {
            "require": {
                "microsoft/windowsazure": "*"
            },          
            "repositories": [
                {
                    "type": "pear",
                    "url": "http://pear.php.net"
                }
            ],
            "minimum-stability": "dev"
        }

3. **[composer.phar][]** をプロジェクトのルートにダウンロードします。

4. コマンド プロンプトを開き、次のコマンドをプロジェクトのルートで実行します。

        php composer.phar install

### 手動でインストールする

Azure 向け PHP クライアント ライブラリを手動でダウンロードしてインストールするには、次のステップに従います。

1. ライブラリが含まれている .zip アーカイブを [GitHub][] からダウンロードします。または、リポジトリをフォークして、ローカル コンピューターにその複製を作成します (この 2 つ目の方法では、GitHub アカウントが必要であるほか、Git がローカルにインストールされている必要があります)。

    <div class="dev-callout"> 
<b>注</b> 
<p>Azure 向け PHP クライアント ライブラリは、<a href="http://pear.php.net/package/HTTP_Request2">HTTP_Request2</a>、<a href="http://pear.php.net/package/Mail_mime">Mail_mime</a>、および <a href="http://pear.php.net/package/Mail_mimeDecode">Mail_mimeDecode</a> の PEAR パッケージに依存しています。これらの依存関係を解決するには、<a href="http://pear.php.net/manual/en/installation.php">PEAR パッケージ マネージャー</a>を使用してこれらのパッケージをインストールすることをお勧めします。</p> 
</div>

2. ダウンロードしたアーカイブの `WindowsAzure` ディレクトリをアプリケーションのディレクトリ構造にコピーします。

(PEAR パッケージとしてのインストールに関する情報を含めて) Azure 向け PHP クライアント ライブラリのインストールの詳細については、[PHP 用 Azure SDK のダウンロードに関するページ][]を参照してください。

  [Git をインストールします]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [composer.phar]: http://getcomposer.org/composer.phar
  [GitHub]: http://go.microsoft.com/fwlink/?LinkId=252719
  [HTTP\_Request2]: http://pear.php.net/package/HTTP_Request2
  [Mail\_mime]: http://pear.php.net/package/Mail_mime
  [Mail\_mimeDecode]: http://pear.php.net/package/Mail_mimeDecode
  [PEAR パッケージ マネージャー]: http://pear.php.net/manual/en/installation.php
  [PHP 用 Azure SDK のダウンロードに関するページ]: ../php-download-sdk/

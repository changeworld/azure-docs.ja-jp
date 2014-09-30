ドメイン名のレコードが反映されると、カスタム ドメイン名を使用して対象の Web サイトにアクセスできることをブラウザーで確認できます。

> [WACOM.NOTE] CNAME が DNS に反映されるまで多少の時間がかかります。<http://www.digwebinterface.com/> などのサービスを使用すると、CNAME を利用できるかどうかを確認できます。

カスタム ドメイン名が Traffic Manager にルーティングされているため、Web サイトが Traffic Manager のエンドポイントとしてまだ追加されていない場合は、名前を適切に解決するためにエンドポイントとして追加する必要があります。これにより、Traffic Manager によって対象の Web サイトにルーティングされます。Traffic Manager のプロファイルに Web サイトをエンドポイントとして追加する方法については、「[エンドポイントの追加と削除][]」を参照してください。

> [WACOM.NOTE] エンドポイントを追加する際に Web サイトが表示されない場合は、Web サイトが標準モードに構成されていることを確認します。Web サイトを Traffic Manager で操作するには、標準モードを使用する必要があります。

  [エンドポイントの追加と削除]: http://msdn.microsoft.com/en-us/library/windowsazure/hh744839.aspx

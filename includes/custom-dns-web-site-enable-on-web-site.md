ドメイン名のレコードが反映されたら、それを Web アプリに関連付ける必要があります。Web ブラウザーを使用してドメイン名を有効にするには、次の手順を使用します。

> [AZURE.NOTE]前の手順で作成された CNAME レコードが DNS に反映されるまで多少の時間がかかります。CNAME が反映されるまで、Web アプリに対してドメイン名を追加することはできません。A レコードを使用している場合には、前の手順で作成された **awverify** CNAME レコードが反映されるまで、Web アプリに A レコードのドメイン名を追加することはできません。
>
> <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> などのサービスを使用すると、CNAME を利用できるかどうかを確認できます。

1. ブラウザーで、[Azure 管理ポータル](https://portal.azure.com)を開きます。

2. **[Web Apps]** タブで、Web アプリの名前をクリックし、**[設定]**、**[カスタム ドメインと SSL]** の順に選択します。

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. **[カスタム ドメインと SSL]** ブレードで **[外部ドメインの使用]** をクリックします。

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4. **[ドメイン名]** ボックスに、この Web アプリに関連付けるドメイン名を入力します。

	![](./media/custom-dns-web-site/dncmntask-cname-8.png)

5. **[保存]** をクリックしてドメイン名の構成を保存します。

	構成が完了すると、カスタム ドメイン名が Web アプリの **[ドメイン名]** セクションに表示されます。

この時点でブラウザーにカスタム ドメイン名を入力して、対象の Web アプリに正常にアクセスできることを確認できます。

<!---HONumber=August15_HO6-->

#### SAS ケーブルの取り付け方法

1. 主エンクロージャと EBOD エンクロージャを識別します。2 つのエンクロージャはそれぞれのバックプレーンを見て識別できます。次の図を参考にしてください。 

    ![主エンクロージャと EBOD エンクロージャの背面](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)

    **主エンクロージャと EBOD エンクロージャの背面**

    |ラベル|説明|
    |:----|:----------|
    |1|主エンクロージャ|
    |2|EBOD エンクロージャ|

2. 主エンクロージャと EBOD エンクロージャのシリアル番号を見つけます。シリアル番号ステッカーが各エンクロージャの背面の耳に付いています。両方のエンクロージャのシリアル番号が一致している必要があります。シリアル番号が一致しない場合は、直ちに [Microsoft サポートに連絡してください](storsimple-contact-microsoft-support.md)。次の図を参考にしてシリアル番号を見つけてください。

    ![シリアル番号の位置を示すエンクロージャの背面図](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)

    **シリアル番号ステッカーの位置**

    |ラベル|説明|
    |:----|:----------|
    |1|エンクロージャの耳|

3. 次のように、付属の SAS ケーブルを使用して EBOD エンクロージャを主エンクロージャに接続します。

    1. 主エンクロージャと EBOD エンクロージャの 4 つの SAS ポートを識別します。SAS ポートには、主エンクロージャでは EBOD、EBOD エンクロージャでは CTRL というラベルが付いています (下の SAS ケーブル配線図を参照)。

    2. 付属の SAS ケーブルを使用して、EBOD ポートを CTRL ポートに接続します。

    3. コントローラー 0 の EBOD ポートを EBOD コントローラー 0 の CTRL ポートに接続する必要があります。コントローラー 1 の EBOD ポートは EBOD コントローラー 1 の CTRL ポートに接続する必要があります。次の図を参考にしてください。
																	
     ![デバイスの SAS ケーブル接続](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)

     **SAS ケーブル接続**

    |ラベル|説明|
    |:----|:----------|
    |A|主エンクロージャ|
    |B|EBOD エンクロージャ|
    |1|コントローラー 0|
    |2|コントローラー 1|
    |3|EBOD コントローラー 0|
    |4|EBOD コントローラー 1|
    |5、6|主エンクロージャの SAS ポート (EBOD のラベル)|
    |7、8|EBOD エンクロージャの SAS ポート (CTRL のラベル)|

<!---HONumber=August15_HO7-->
<properties  linkid="Install-Config-Windows-Azure-PowerShell" urlDisplayName="Azure PowerShell" pageTitle="How to install and configure Azure PowerShell" description="Learn how to install and configure Azure PowerShell." umbracoNaviHide="0" disqusComments="1" editor="tysonn" manager="jeffreyg" documentationCenter="" services="" solutions="" authors="kathydav" title="How to install and configure Azure PowerShell" />
# Installieren und Konfigurieren von Azure PowerShell

 
<div  class="dev-center-tutorial-selector sublanding"><a  href="/en-us/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a  href="/en-us/manage/install-and-configure-cli/" title="Plattformübergreifende Befehlszeilenschnittstelle">Plattformübergreifende Befehlszeilenschnittstelle</a></div>

 Mit Windows PowerShell können Sie eine Vielzahl von Aufgaben in Azure
interaktiv an einer Eingabeaufforderung oder automatisch durch Skripte
durchführen. Azure PowerShell ist ein Modul, das Cmdlets zum Verwalten
von Azure durch Windows PowerShell enthält. Sie können diese Cmdlets
verwenden, um durch die Azure-Plattform bereitgestellte Lösungen und
Dienste zu erstellen, zu testen, bereitzustellen und zu verwalten. In
den meisten Fällen können Sie mit den Cmdlets dieselben Aufgaben
ausführen wie über das Azure-Verwaltungsportal. Beispielsweise können
Sie Clouddienste, virtuelle Computer, virtuelle Netzwerke und Websites
erstellen und konfigurieren.

Das Modul wird als herunterladbare Datei verteilt und der Quellcode wird
in einem öffentlich verfügbaren Repository verwaltet. Weiter unten in
diesem Thema wird in den Installationsanweisungen ein Link zu den
herunterladbaren Dateien bereitgestellt. Informationen zum Quellcode
finden Sie im [Azure PowerShell-Coderepository][1].

Dieser Leitfaden enthält grundlegende Informationen zum Installieren und
Einrichten von Azure PowerShell, um die Azure-Plattform zu verwalten.
## Inhaltsverzeichnis

* [Erforderliche Komponenten für die Verwendung von Azure
  PowerShell](#Prereq)
* [Gewusst wie: Installieren von Azure PowerShell](#Install)
* [Gewusst wie: Verbinden mit Ihrem Abonnement](#Connect)
* [Verwenden der Cmdlets: Beispiel](#Ex)
* [Hilfe](#Help)
* [Zusätzliche Ressourcen](#Resources)
### <a  id="Prereq" ></a>Erforderliche Komponenten für die Verwendung von Azure PowerShell

Azure ist eine abonnementbasierte Plattform. Das bedeutet, dass zur
Verwendung der Plattform ein Abonnement benötigt wird. In den meisten
Fällen werden für die Cmdlets Abonnementinformationen benötigt, um die
Aufgaben mit Ihrem Abonnement auszuführen. (Einige der speicherbezogenen
Cmdlets können ohne diese Informationen verwendet werden.) Sie stellen
sie zur Verfügung, indem Sie die Verbindung mit Ihrem Abonnement auf
Ihrem Computer konfigurieren. Anweisungen dazu finden Sie in diesem
Artikel unter "Gewusst wie: Verbinden mit Ihrem Abonnement".

> [WACOM.NOTE] Es ist eine Vielzahl von Abonnementoptionen verfügbar.
> Weitere Informationen finden Sie unter [Erste Schritte mit Azure][2].

Bei der Installation des Moduls wird vom Installationsprogramm geprüft,
ob das System über die erforderliche Software verfügt, und es werden
alle Abhängigkeiten installiert, z. B. die richtige Version von Windows
PowerShell und .NET Framework.

<h2> <a  id="Install" ></a>Gewusst wie: Installieren von Azure PowerShell</h2>


Sie können das Azure PowerShell-Modul herunterladen und installieren,
indem Sie den [Microsoft-Webplattform-Installer][3] ausführen. Klicken
Sie in der Nachfrage auf **Ausführen**. Der
Microsoft-Webplattform-Installer wird geladen, und das Modul **Azure
PowerShell** ist zur Installation verfügbar. Alle Abhängigkeiten für die
Azure PowerShell-Cmdlets werden durch den Webplattform-Installer
installiert. Befolgen Sie die Anweisungen, um die Installation
abzuschließen.

Weitere Informationen zu den für Azure verfügbaren Befehlszeilentools
finden Sie unter [Befehlszeilentools][4].

Bei der Installation des Moduls wird auch eine benutzerdefinierte
Konsole für Azure PowerShell installiert. Sie können die Cmdlets über
die Windows PowerShell-Standardkonsole oder über die Azure
PowerShell-Konsole ausführen.

Die Methode, die Sie zum Öffnen einer der beiden Konsolen verwenden, ist
abhängig von der ausgeführten Windows-Version:

* Auf einem Computer, auf dem mindestens Windows 8 oder Windows
  Server 2012 ausgeführt wird, können Sie die integrierte Suche
  verwenden. Beginnen Sie auf dem Startbildschirm mit der Eingabe von
  **power**. Daraufhin wird eine entsprechende Liste von Anwendungen
  erstellt, die auch Windows PowerShell und Azure PowerShell umfasst.
  Klicken Sie auf eine dieser beiden Anwendungen, um das Konsolenfenster
  zu öffnen. (Klicken Sie mit der rechten Maustaste auf das Symbol, um
  die Anwendung an den Startbildschirm anzuheften.)

* Auf einem Computer, auf dem eine niedrigere Version als Windows 8 oder
  Windows Server 2012 ausgeführt wird, können Sie das Startmenü
  verwenden. Klicken Sie im Startmenü auf **Alle Programme** >
  **Azure** und dann auf **Azure PowerShell**.

<h2><a  id="Connect" ></a>Gewusst wie: Verbinden mit Ihrem Abonnement</h2>


Zur Verwendung von Azure wird ein Abonnement benötigt. Wenn Sie über
kein Abonnement verfügen, lesen Sie die Informationen unter [Erste
Schritte mit Azure][2].

Für die Cmdlets werden Ihre Abonnementinformationen benötigt, damit Sie
Ihre Dienste über das Abonnement verwalten können. Ab Version .0.7 des
Moduls gibt es zwei Möglichkeiten, um diese Informationen anzugeben. Sie
können ein Verwaltungszertifikat mit den Informationen herunterladen und
verwenden oder sich mit Ihrem Microsoft-Konto oder einer
Organisations-ID bei Azure anmelden. Bei der Anmeldung werden die
Anmeldeinformationen von Azure Active Directory (Azure AD)
authentifiziert.

Beachten Sie bezüglich der Auswahl der geeigneten
Authentifizierungsmethode Folgendes:

* Azure AD kann die Verwaltung des Zugriffs auf ein Abonnement
  vereinfachen, es kann jedoch die Automatisierung behindern. Die
  Anmeldeinformationen sind 12 Stunden lang für Azure PowerShell
  verfügbar. Nach ihrem Ablauf müssen Sie sich erneut anmelden.
* Bei Verwendung eines Zertifikats sind die Abonnementinformationen für
  den gesamten Gültigkeitszeitraum des Abonnements und Zertifikats
  verfügbar. Auf diese Weise ist es einfacher, Automatisierung für
  Aufgaben mit langer Ausführungszeit einzusetzen. Nach dem
  Herunterladen und Importieren der Informationen müssen Sie diese nicht
  erneut angeben. Diese Methode erschwert jedoch die Verwaltung des
  Zugriffs auf ein freigegebenes Abonnement, etwa, wenn mehr als eine
  Person für den Zugriff auf das Konto berechtigt ist.

Weitere Informationen zur Authentifizierungs- und Abonnemontverwaltung
in Azure finden Sie unter [Verwalten von Konten, Abonnements und
Administratorrollen][5].

<h3>Verwenden von Azure AD</h3>


1.  Öffnen Sie die Azure PowerShell-Konsole, wie unter [Gewusst wie:
    Installieren von Azure PowerShell](#Install) beschrieben.

2.  Geben Sie den folgenden Befehl ein:
    
    `Add-AzureAccount`

3.  Geben Sie in dem Fenster die dem Konto zugeordnete E-Mail-Adresse
    und das zugehörige Kennwort ein.

4.  Die Anmeldeinformationen werden von Azure authentifiziert und
    gespeichert, dann wird das Fenster geschlossen.

<h3>Verwenden eines Zertifikats</h3>


Das Azure PowerShell-Modul enthält Cmdlets, mit denen Sie das Zertifikat
herunterladen und importieren können.

* Mit dem Cmdlet **Get-AzurePublishSettingsFile** wird eine Webseite im
  Azure-Verwaltungsportal geöffnet, von der Sie die
  Abonnementinformationen herunterladen können. Die Informationen
  befinden sich in einer Datei mit der Erweiterung \*.publishsettings.

* Die Datei mit der Erweiterung \*.publishsettings wird mit
  **Import-AzurePublishSettingsFile** zur Verwendung durch das Modul
  importiert. Diese Datei enthält ein Verwaltungszertifikat mit
  Sicherheitsanmeldeinformationen.
<div class="dev-callout"> 
<b>Wichtig</b> 
<p>Es empfiehlt sich, das mithilfe von <b>Get-AzurePublishSettingsFile</b> heruntergeladene Veröffentlichungsprofil nach dem Import dieser Einstellungen zu löschen. Da das Verwaltungszertifikat Sicherheitsanmeldeinformationen enthält, sollte nicht von nicht autorisierten Benutzern darauf zugegriffen werden. Wenn Sie Informationen zu Ihren Abonnements benötigen, können Sie diese im <a  href="http://manage.windowsazure.com/">Azure-Verwaltungsportal</a> oder <a  href="http://go.microsoft.com/fwlink/p/?LinkId=324875">Microsoft Online Services-Kundenportal</a> einsehen.</p> 
</div>

 1.  Melden Sie sich mit den Anmeldeinformationen für Ihr Azure-Konto
    beim [Azure-Verwaltungsportal][6] an.

2.  Öffnen Sie die Azure PowerShell-Konsole, wie unter [Gewusst wie:
    Installieren von Azure PowerShell](#Install) beschrieben.

3.  Geben Sie den folgenden Befehl ein:
    
    `Get-AzurePublishSettingsFile`

4.  Laden Sie das Veröffentlichungsprofil herunter und speichern Sie es,
    wenn Sie eine entsprechende Aufforderung erhalten, und notieren Sie
    den Pfad und Namen der Datei mit der Erweiterung \*.publishsettings.
    Diese Informationen werden benötigt, wenn Sie das Cmdlet
    **Import-AzurePublishSettingsFile** ausführen, um die Einstellungen
    zu importieren. Für den Standardspeicherort und -dateinamen gilt
    folgendes Format:
    
    C:\\Benutzer\\<Benutzerprofil>\\Download\\[*MeinAbonnement*-...]-*Downloaddatum*-credentials.publishsettings

5.  Geben Sie einen Befehl wie den folgenden ein, wobei Sie die
    Platzhalter durch Ihren Windows-Kontonamen und den Pfad und
    Dateinamen ersetzen:
    
    Import-AzurePublishSettingsFile
    C:\\Benutzer\\<Benutzerprofil>\\Downloads\\<Abonnementname>-credentials.publishsettings

> [WACOM.NOTE] Wenn Sie nach dem Import Ihrer
> Veröffentlichungseinstellungen als Co-Administrator zu anderen
> Abonnements hinzugefügt werden, müssen Sie diesen Vorgang wiederholen,
> um eine neue Datei mit der Erweiterung \*.publishsettings
> herunterzuladen und dann diese Einstellungen zu importieren.
> Informationen zum Hinzufügen von Co-Administratoren als Unterstützung
> bei der Verwaltung von Diensten für ein Abonnement finden Sie unter
> [Hinzufügen und Entfernen von Co-Administratoren für
> Azure-Abonnements][7].

<h3> Anzeigen von Konto- und Abonnementinformationen</h3>


Sie können über mehrere Konten und Abonnements zur Verwendung durch
Azure PowerShell verfügen und mehrere Konten hinzufügen, indem Sie
**Add-AzureAccount** mehr als einmal ausführen.

Geben Sie Folgendes ein, um die verfügbaren Konten anzuzeigen:

    `Get-AzureAccount`

Geben Sie Folgendes ein, um die Abonnementinformationen anzuzeigen:

    `Get-AzureSubscription`
## <a  id="Ex" ></a>Verwenden der Cmdlets: Beispiel

Nachdem Sie das Modul installiert und die Verbindung zwischen Ihrem
Computer und dem Abonnement konfiguriert haben, können Sie als Beispiel
für die Verwendung der Cmdlets eine Website erstellen.

1.  Öffnen Sie die Azure PowerShell-Shell, falls diese noch nicht
    geöffnet ist.

2.  Legen Sie einen Namen für die Website fest. Sie müssen einen Namen
    wählen, der den DNS-Benennungskonventionen entspricht. Gültige Namen
    dürfen nur Buchstaben von a bis z, Ziffern von 0 bis 9 und
    Bindestriche (-) enthalten. Darüber hinaus muss der Name in Azure
    eindeutig sein.
    
    Geben Sie einen Befehl wie den folgenden ein, nachdem Sie einen
    Namen ausgewählt haben. Um etwa eine Website mit Ihrem Konto und
    einer Ziffer zu erstellen, damit diese Konvention mehr als einmal
    verwendet werden kann, geben Sie Folgendes ein und setzen Sie dabei
    Ihren eigenen Kontonamen ein:
    
    `New-AzureWebsite Mein-Websitename-1`
    
    Die Website wird vom Cmdlet erstellt. Daraufhin werden Informationen
    dazu aufgeführt.

3.  Geben Sie Folgendes ein, um den Status der Website zu überprüfen:
    
    `Get-AzureWebsite`
    
    Der Name und Status sowie der Hostname der neuen Website werden vom
    Cmdlet aufgeführt.
    
    > [WACOM.NOTE] Wenn der Befehl wie gezeigt ausgeführt wird, werden
    > Informationen zu allen Websites aufgeführt, die dem aktuellen
    > Abonnement zugeordnet sind.

4.  Websites werden nach der Erstellung gestartet. Geben Sie Folgendes
    ein, um die Website zu beenden:
    
    `Stop-AzureWebsite -Name Kontoname-1`

5.  Führen Sie den Befehl **Get-AzureWebsite** erneut aus, um zu
    überprüfen, ob die Website beendet wurde.

6.  Sie können die Website löschen, um diesen Test abzuschließen. Geben
    Sie Folgendes ein:
    
    `Remove-AzureWebsite -Name Kontoname-1`
    
    Bestätigen Sie den Löschvorgang, um die Aufgabe abzuschließen.
## <a  id="Help" ></a>Hilfe

Diese Ressourcen enthalten Hilfethemen für bestimmte Cmdlets:

* Sie können in der Konsole das integrierte Hilfesystem verwenden. Das
  Cmdlet **Get-Help** ermöglicht den Zugriff auf dieses System. Die
  folgende Tabelle enthält einige Beispiele für Befehle, die Sie
  verwenden können, um Hilfe anzufordern. Sie können weitere
  Informationen in der Konsole erhalten, indem Sie **help** eingeben.
  
  <table  border="1" cellspacing="4" cellpadding="4">
  <tbody>
  <tr  align="left" valign="top">
      <td><b>Befehl</b>
  </td>
  
      <td><b>Ergebnis</b>
  </td>
  
  </tr>
  
  <tr  align="left" valign="top">
      <td>help</td>
  
      <td>Beschreibt die Verwendung des Hilfesystems. <p><b>Hinweis</b>
  : Die Beschreibung enthält auch Informationen zu Hilfedateien, die sich nicht auf das Azure-Modul beziehen. Insbesondere werden bei der Installation des Moduls auch Hilfedateien installiert. Sie können nicht separat heruntergeladen werden.</p>
  
  </td> </tr> <tr  align="left"
valign="top"> <td >help azure</td>
<td >Listet alle Cmdlets im Azure
PowerShell-Modul auf.</td> </tr> <tr 
align="left" valign="top"> <td >help
<b>Sprache</b>-dev</td> <td
>Listet Cmdlets zum Entwickeln und Verwalten von
Anwendungen in einer bestimmten Sprache auf. Beispiel: help node-dev,
help php-dev, help python-dev.</td> </tr> <tr
 align="left" valign="top"> <td
>help <b>Cmdlet</b></td> <td >Zeigt
die Hilfe zu einem Windows PowerShell-Cmdlet an.</td> </tr>
<tr  align="left" valign="top"> <td
>help <b>Cmdlet</b>
-parameter </td> <td >Zeigt
Parameterdefinitionen für ein Cmdlet an. Beispiel: help
get-azuresubscription -parameter \*</td> </tr> <tr
 align="left" valign="top"> <td
>help <b>Cmdlet</b>
-examples</td> <td > Zeigt die Syntax und
Beschreibung von Beispielbefehlen für ein Cmdlet an.</td>
</tr> <tr  align="left" valign="top">
<td >help <b>Cmdlet</b> -full</td> <td
>Zeigt technische Anforderungen für ein Cmdlet
an.</td> </tr> </tbody> </table>

* Referenzinformationen zu den Cmdlets im Azure PowerShell-Modul sind in
  der Azure-Bibliothek verfügbar. Informationen finden Sie in der
  [Azure-Cmdlet-Referenz][8].

Hilfe von der Community erhalten Sie in den folgenden beliebten Foren:

* [Azure-Forum auf MSDN][9]
* [Stackoverflow][10]
## <a  id="Resources" ></a>Weitere Ressourcen

Im Folgenden werden einige der verfügbaren Ressourcen genannt, in denen
Sie erfahren, wie Azure und Windows PowerShell verwendet werden.

* Um Feedback zu Cmdlets zu geben, Probleme zu melden oder auf den
  Quellcode zuzugreifen, rufen Sie das [Azure
  PowerShell-Coderepository][1] auf.

* Informationen zur Windows PowerShell-Befehlszeile und
  -skriptingumgebung finden Sie im [TechNet Script Center][11].

* Informationen zum Installieren, Erlernen, Verwenden und Anpassen von
  Windows PowerShell finden Sie unter [Skripterstellung mit Windows
  PowerShell][12].

* Informationen zu Skripten und ihrer Ausführung in Windows PowerShell
  finden Sie unter [Ausführen von Skripten][13]. Dieser Artikel enthält
  grundlegende Informationen zum Erstellen von Skripten und
  Konfigurieren des Computers zur Ausführung von Skripten.

* Informationen zu Cmdlets für Azure AD finden Sie unter [Verwalten von
  Azure AD mithilfe von Windows PowerShell][14].



[1]: https://github.com/WindowsAzure/azure-sdk-tools
[2]: http://go.microsoft.com/fwlink/p/?LinkId=320795
[3]: http://go.microsoft.com/fwlink/p/?LinkId=320376
[4]: http://go.microsoft.com/fwlink/?LinkId=320796
[5]: http://go.microsoft.com/fwlink/?LinkId=324796
[6]: http://manage.windowsazure.com
[7]: http://msdn.microsoft.com/en-us/library/windowsazure/gg456328.aspx
[8]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554330.aspx
[9]: http://go.microsoft.com/fwlink/p/?LinkId=320212
[10]: http://go.microsoft.com/fwlink/?LinkId=320213
[11]: http://go.microsoft.com/fwlink/p/?LinkId=320211
[12]: http://go.microsoft.com/fwlink/p/?LinkId=320210
[13]: http://go.microsoft.com/fwlink/p/?LinkId=320627
[14]: http://go.microsoft.com/fwlink/p/?LinkId=320628

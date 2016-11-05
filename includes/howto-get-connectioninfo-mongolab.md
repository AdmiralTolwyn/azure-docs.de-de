Wenn Sie eine MongoLab-Datenbank bereitstellen, sendet MongoLab einen Verbindungs-URI im MongoLab-Standardformat für Verbindungszeichenfolgen an Azure. Mithilfe dieses Werts wird eine MongoDB-Verbindung über den von Ihnen ausgewählten MongoDB-Treiber hergestellt. Weitere Informationen über Verbindungszeichenfolgen erhalten Sie auf mongodb.org unter [Verbindungen](http://www.mongodb.org/display/DOCS/Connections).

**Dieser URI enthält Ihren Benutzernamen und Ihr Kennwort für die Datenbank. Behandeln Sie diese Angaben als kritische Informationen, und teilen Sie sie niemandem mit.**

Gehen Sie folgendermaßen vor, um diesen URI im Azure-Portal abzurufen:

1. Klicken Sie auf **Add-Ons**. ![AddonsButton][button-addons]
2. Suchen Sie in Ihrer Add-On-Liste nach Ihrem MongoLab-Dienst. ![MongolabEntry][entry-mongolabaddon]
3. Klicken Sie auf den Namen Ihres Add-Ons, um auf die Add-On-Seite zu gelangen.
4. Klicken Sie auf **Verbindungsinformationen**. ![ConnectionInfoButton][button-connectioninfo] Ihr MongoLab-URI wird angezeigt: ![ConnectionInfoScreen][screen-connectioninfo]  
5. Klicken Sie auf die Schaltfläche rechts neben dem Wert MONGOLAB\_URI, um den vollständigen Wert in die Zwischenablage zu kopieren.

[entry-mongolabaddon]: ./media/howto-get-connectioninfo-mongolab/entry-mongolabaddon.png
[button-connectioninfo]: ./media/howto-get-connectioninfo-mongolab/button-connectioninfo.png
[screen-connectioninfo]: ./media/howto-get-connectioninfo-mongolab/dialog-mongolab_connectioninfo.png
[button-addons]: ./media/howto-get-connectioninfo-mongolab/button-addons.png

<!---HONumber=Oct15_HO3-->
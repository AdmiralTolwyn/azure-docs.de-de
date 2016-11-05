
1. Klicken Sie auf dem Blatt **Hybridverbindungen** auf die Hybridverbindung, die Sie gerade erstellt haben, und klicken Sie dann auf **Listenereinrichtung**.
   
    ![Listener-Einrichtung](./media/app-service-hybrid-connections-manager-install/D04ClickListenerSetup.png)
2. Der Fensterbereich **Hybrid connection properties** wird geöffnet. Wählen Sie unter **Manager für lokale Hybridverbindungen** die Option **Herunterladen und manuell konfigurieren** aus, speichern Sie das heruntergeladene Paket „HybridConnectionManager.msi“, und kopieren Sie die Gatewayverbindungszeichenfolge.
   
    ![Zum Installieren hier klicken](./media/app-service-hybrid-connections-manager-install/D05ClickToInstallHCM.png)
3. Geben Sie an der Administratoreingabeaufforderung den folgenden Befehl ein, um das Installationsprogramm zu starten:
   
        start HybridConnectionManager.msi
4. Klicken Sie nach Ausführung des Installationsprogramms auf **Nicht jetzt**, navigieren Sie zum Ordner "%ProgramFiles%\\Microsoft\\HybridConnectionManager", führen Sie die Datei "HCMConfigWizard.exe" aus, und klicken Sie im Dialogfeld **Benutzerkontensteuerung** auf **Ja**.
5. Fügen Sie die Hybridverbindungszeichenfolge ein, die Sie zuvor kopiert haben, und klicken Sie auf **OK**.
   
    ![Installation](./media/app-service-hybrid-connections-manager-install/D08aHCMInstallManual.png)
6. Sobald die Installation abgeschlossen ist, klicken Sie auf **Close**.
   
    ![Klicken Sie auf "Schließen"](./media/app-service-hybrid-connections-manager-install/D09HCMInstallComplete.png)
   
    Im Fensterbereich **Hybrid connections** zeigt die Spalte **Status** jetzt **Connected** an.
   
    ![Status Verbunden](./media/app-service-hybrid-connections-manager-install/D10HCStatusConnected.png)

<!---HONumber=AcomDC_1125_2015-->
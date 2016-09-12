## Erstellen von VNets mithilfe von PowerShell
Führen Sie zum Erstellen von VNets mithilfe von PowerShell die folgenden Schritte aus.

1. Wenn Sie Azure PowerShell zuvor noch nicht verwendet haben, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](../articles/powershell-install-configure.md), und befolgen Sie die komplette Anleitung, um sich bei Azure anzumelden und Ihr Abonnement auszuwählen.
	
2. Erstellen Sie ggf. eine neue Ressourcengruppe, wie unten dargestellt. In diesem Szenario erstellen Sie eine Ressourcengruppe mit dem Namen *TestRG*. Weitere Informationen zu Ressourcengruppen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](../articles/resource-group-overview.md).

		New-AzureRmResourceGroup -Name TestRG -Location centralus

	Erwartete Ausgabe:
	
		ResourceGroupName : TestRG
		Location          : centralus
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG	

3. Erstellen Sie ein neues VNet namens *TestVNet*, wie unten dargestellt.

		New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
			-AddressPrefix 192.168.0.0/16 -Location centralus	
		
	Erwartete Ausgabe:

		Name              	: TestVNet
		ResourceGroupName 	: TestRG
		Location          	: centralus
		Id                	: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag           		: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState      	: Succeeded
		Tags                   	: 
		AddressSpace           	: {
		                           "AddressPrefixes": [
		                             "192.168.0.0/16"
		                           ]
                         		 }
		DhcpOptions            	: {}
		Subnets                	: []
		VirtualNetworkPeerings 	: []

4. Speichern Sie das virtuelle Netzwerkobjekt in einer Variablen, wie unten dargestellt.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
	
	>[AZURE.TIP] Sie können die Schritte 3 und 4 durch Ausführen von **$vnet = New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus** zusammenfassen.

5. Fügen Sie der neuen VNet-Variablen ein Subnetz hinzu, wie unten dargestellt.

		Add-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
		
	Erwartete Ausgabe:

		Name              	: TestVNet
		ResourceGroupName 	: TestRG
		Location          	: centralus
		Id                	: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              	: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState 	: Succeeded
		Tags              	:
		AddressSpace      	: {
			                      "AddressPrefixes": [
			                        "192.168.0.0/16"
			                      ]
			                    }
		DhcpOptions       	: {}
		Subnets         	: [
			                      {
			                        "Name": "FrontEnd",
			                        "AddressPrefix": "192.168.1.0/24"
			                      }
			                    ]
		VirtualNetworkPeerings 	: []

6. Wiederholen Sie Schritt 5 für jedes Subnetz, das Sie erstellen möchten. Der folgende Befehl erstellt das *BackEnd*-Subnetz für dieses Szenario.

		Add-AzureRmVirtualNetworkSubnetConfig -Name BackEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24

7. Obwohl Sie Subnetze erstellen, sind diese derzeit nur in der lokalen Variablen enthalten, mit der das in Schritt 4 erstellte VNet abgerufen wird. Führen Sie zum Speichern der Änderungen in Azure das Cmdlet **Set-AzureRmVirtualNetwork** aus, wie unten dargestellt.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet	
		
	Erwartete Ausgabe:

		Name              	: TestVNet
		ResourceGroupName 	: TestRG
		Location          	: centralus
		Id                	: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              	: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState 	: Succeeded
		Tags              	:
		AddressSpace      	: {
			                      "AddressPrefixes": [
			                        "192.168.0.0/16"
			                      ]
			                    }
		DhcpOptions       	: {
			                      "DnsServers": []
			                    }
		Subnets           	: [
			                      {
			                        "Name": "FrontEnd",
			                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
			                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
			                        "AddressPrefix": "192.168.1.0/24",
			                        "IpConfigurations": [],
			                        "ProvisioningState": "Succeeded"
			                      },
			                      {
			                        "Name": "BackEnd",
			                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
			                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
			                        "AddressPrefix": "192.168.2.0/24",
			                        "IpConfigurations": [],
			                        "ProvisioningState": "Succeeded"
			                      }
			                    ]
		VirtualNetworkPeerings : []

<!---HONumber=AcomDC_0831_2016-->
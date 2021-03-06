---
title: "Entorno de prueba de nube híbrida simulado | Microsoft Docs"
description: "Cree un entorno de nube híbrida simulado para profesionales de TI o pruebas de desarrollo con dos redes virtuales de Azure y una conexión de red virtual a red virtual."
services: virtual-machines-windows
documentationcenter: 
author: JoeDavies-MSFT
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: ca5bf294-8172-44a9-8fed-d6f38d345364
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: josephd
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 4ec6f079b762a25894d822bfc098ea5442a1f7e4
ms.lasthandoff: 03/31/2017


---
# <a name="set-up-a-simulated-hybrid-cloud-environment-for-testing"></a>Configuración de un entorno de nube híbrida simulado para pruebas
Este artículo le guiará en el proceso de creación de un entorno de nube híbrida simulado con Microsoft Azure mediante dos redes virtuales de Azure. Aquí está la configuración resultante.

![](./media/ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)

Esto simula un entorno de producción en la nube híbrida y consta de los siguientes elementos:

* Una red local simulada y simplificada hospedada en una red virtual de Azure (la red virtual TestLab).
* Una red virtual simulada entre locales hospedada en Azure (TestVNET).
* Una conexión de red virtual a red virtual entre las dos redes virtuales.
* Un controlador de dominio secundario en la red virtual TestVNET.

Esto proporciona una base y un punto de partida común desde el que puede:

* Desarrollar y probar las aplicaciones en un entorno de nube híbrida simulado.
* Cree configuraciones de prueba de los equipos, algunos dentro de la red virtual de TestLab y otros en la red virtual de TestVNET, para simular cargas de trabajo de TI basadas en la nube híbrida.

Existen cuatro fases principales para configurar este entorno de prueba de nube híbrida:

1. Configuración de la red virtual de TestLab.
2. Creación de la red virtual entre locales.
3. Creación de la conexión VPN de red virtual a red virtual.
4. Configuración de DC2. 

Esta configuración requiere una suscripción de Azure. Si tiene una suscripción de MSDN o de Visual Studio, consulte [Crédito mensual de Azure para suscriptores de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

> [!NOTE]
> Las máquinas virtuales y las puertas de enlace de redes virtuales en Azure incurren en un coste económico constante cuando se están ejecutando. Este costo se factura en su suscripción de pago o MSDN. Una puerta de enlace de VPN de Azure se implementa como un conjunto de dos máquinas virtuales de Azure. Para minimizar los costos, hay que crear el entorno de prueba y realizar las pruebas y demostraciones necesarias lo más rápido posible.
> 
> 

## <a name="phase-1-configure-the-testlab-virtual-network"></a>Fase 1: configuración de la red virtual TestLab
Use las instrucciones que encontrará en el tema [Entorno de prueba de la configuración base](https://technet.microsoft.com/library/mt771177.aspx) para configurar los equipos DC1, APP1 y CLIENT1 de una red virtual de Azure denominada "TestLab". 

Después, inicie un símbolo del sistema de Azure PowerShell.

> [!NOTE]
> El siguiente comando establece el uso de Azure PowerShell 1.0 y versiones posteriores.
> 
> 

Inicie sesión en su cuenta.

    Login-AzureRMAccount

Obtenga el nombre de la suscripción con el comando siguiente.

    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Establezca su suscripción a Azure. Utilice la misma suscripción que usó para generar la configuración básica de la fase 1. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < y >, por los nombres correctos.

    $subscr="<subscription name>"
    Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Después, agregue una subred de puerta de enlace a la red virtual TestLab de la configuración básica, que se utilizará para hospedar la puerta de enlace de Azure.

    $rgName="<name of your resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $vnet=Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name TestLab
    Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 10.255.255.248/29 -VirtualNetwork $vnet
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

Después, solicite una dirección IP pública para asignarla a la puerta de enlace para la red virtual TestLab.

    $gwpip=New-AzureRmPublicIpAddress -Name TestLab_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

A continuación, cree la puerta de enlace.

    $vnet=Get-AzureRmVirtualNetwork -Name TestLab -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name TestLab_GWConfig -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 
    New-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Tenga en cuenta que las puertas de enlace nuevas pueden tardar 20 minutos o más en crearse.

Desde el Portal de Azure en el equipo local, conéctese a DC1 con las credenciales CORP\User1. Para configurar el dominio CORP para que los usuarios y equipos usen su controlador de dominio local para la autenticación, ejecute estos comandos desde un símbolo del sistema de Windows PowerShell con DC1.

    New-ADReplicationSite -Name "TestLab" 
    New-ADReplicationSite -Name "TestVNET"
    New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
    New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET"

Se trata de la configuración actual.

![](./media/ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph1.png)

## <a name="phase-2-create-the-testvnet-virtual-network"></a>Fase 2: creación de la red virtual TestVNET
En primer lugar, cree la red virtual TestVNET y protéjala con un grupo de seguridad de red.

    $rgName="<name of the resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $locShortName="<Azure location name from $locName in all lowercase letters with spaces removed. Example:  westus>"
    $testSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix 192.168.0.0/24
    $gatewaySubnet=New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 192.168.255.248/29
    New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix 192.168.0.0/16 -Subnet $testSubnet,$gatewaySubnet –DNSServer 10.0.0.4
    $rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
    New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
    $vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name TestVNET
    $nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
    Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet" -AddressPrefix 192.168.0.0/24 -NetworkSecurityGroup $nsg

A continuación, solicite una dirección IP pública que se asignará a la puerta de enlace de la red virtual TestVNET y cree la puerta de enlace.

    $gwpip=New-AzureRmPublicIpAddress -Name TestVNET_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $vnet=Get-AzureRmVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name "TestVNET_GWConfig" -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
    New-AzureRmVirtualNetworkGateway -Name "TestVNET_GW" -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Se trata de la configuración actual.

![](./media/ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph2.png)

## <a name="phase-3-create-the-vnet-to-vnet-connection"></a>Fase 3: creación de la conexión red virtual a red virtual
En primer lugar, solicite una clave previamente compartida aleatoria criptográficamente segura de 32 caracteres al administrador de red o de seguridad. Como alternativa, use la información que encontrará en [Create a random string for an IPsec preshared key](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx) (Creación de una cadena aleatoria para una clave previamente compartida IPsec) para obtener una clave previamente compartida.

Después, use estos comandos para crear la conexión VPN de sitio a sitio, que puede tardar algún tiempo en crearse.

    $sharedKey="<pre-shared key value>"
    $gwTestLab=Get-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName
    $gwTestVNET=Get-AzureRmVirtualNetworkGateway -Name TestVNET_GW -ResourceGroupName $rgName
    New-AzureRmVirtualNetworkGatewayConnection -Name TestLab_to_TestVNET -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestLab -VirtualNetworkGateway2 $gwTestVNET -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey
    New-AzureRmVirtualNetworkGatewayConnection -Name TestVNET_to_TestLab -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestVNET -VirtualNetworkGateway2 $gwTestLab -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey

Después de unos minutos, se debe establecer la conexión.

Se trata de la configuración actual.

![](./media/ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph3.png)

## <a name="phase-4-configure-dc2"></a>Fase 4: configuración de DC2
Primero, cree una máquina virtual para DC2. Ejecute estos comandos en el símbolo del sistema de Azure PowerShell en el equipo local.

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<the storage account name for the base configuration>"
    $vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.0.4
    $vm=New-AzureRMVMConfig -VMName DC2 -VMSize Standard_A1
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestVNET-ADDSDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC2 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name DC2-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Después, inicie sesión en la nueva máquina virtual de DC2 desde el Portal de Azure.

A continuación, configure una regla del Firewall de Windows para permitir el tráfico para probar la conectividad básica. Desde un símbolo del sistema de Windows PowerShell con nivel de administrador en DC2, ejecute estos comandos.

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc1.corp.contoso.com

El comando de ping debería devolver cuatro respuestas correctas desde la dirección IP 10.0.0.4. Esto es una prueba de tráfico a través de la conexión de red virtual a red virtual.

Después, agregue el disco de datos adicional en DC2 como un nuevo volumen con la letra de unidad F:.

1. En el panel izquierdo del Administrador de servidores, haga clic en **Servicios de archivos y almacenamiento** y luego haga clic en **Discos**.
2. En el panel de contenido, en el grupo **Discos**, haga clic en **disco 2** (con la **partición** establecida en **Desconocida**).
3. Haga clic en **Tareas** y luego haga clic en **Nuevo volumen**.
4. En la página Antes de empezar del Asistente para volumen nuevo, haga clic en **Siguiente**.
5. En la página Selección del servidor y del disco, haga clic en **Disco 2** y, a continuación, haga clic en **Siguiente**. Cuando se le solicite, haga clic en **Aceptar**.
6. En la página Especificación del tamaño de la página de volumen, haga clic en **Siguiente**.
7. En la página Asignación a una letra de unidad o carpeta, haga clic en **Siguiente**.
8. En la página Selección de la configuración del sistema de archivos, haga clic en **Siguiente**.
9. En la página Confirmación de las selecciones, haga clic en **Crear**.
10. Cuando haya terminado, haga clic en **Cerrar**.

A continuación, configure DC2 como un controlador de dominio de réplica para el dominio corp.contoso.com. Ejecute estos comandos desde el símbolo del sistema de Windows PowerShell en DC2.

    Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
    Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Tenga en cuenta que se le pedirá que proporcione la contraseña de CORP\User1, una contraseña de Modo de restauración de servicios de directorio (DSRM) y que reinicie DC2.

Ahora que la red virtual TestVNET tiene su propio servidor DNS (DC2), debe configurar la red virtual de TestVNET para utilizar este servidor DNS.

1. En el panel izquierdo del Portal de Azure, haga clic en el icono de las redes virtuales y elija **TestVNET**.
2. En la pestaña **Configuración**, haga clic en **Servidores DNS**.
3. En **Servidor DNS principal**, escriba **192.168.0.4** para sustituir el valor 10.0.0.4.
4. Haga clic en **Guardar**.

Se trata de la configuración actual. 

![](./media/ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)

Su entorno de nube híbrida simulado ya está listo para las pruebas.

## <a name="next-step"></a>Paso siguiente
* Configure una [aplicación de línea de negocio web](ps-hybrid-cloud-test-env-lob.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) en este entorno.



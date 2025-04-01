# OPS_CH3
# week-2-ops3labs.


{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vnet1Name": {
            "type": "string",
            "metadata": {
                "description": "description",
                "displayName": "CoreServiceVnet"
            },
            "defaultValue": "CoreServiceVnet"
        },
        "vnet1subnetOneName": {
            "type": "string",
            "metadata": {
                "description": "description",
                "displayName": "SharedServiceSubnet"
            },
            "defaultValue": "SharedServiceSubnet"
        },
        "vnet1subnetTwoName": {
            "type": "string",
            "metadata": {
                "description": "description",
                "displayName": "DatabaseSubnet"
            },
            "defaultValue": "DatabaseSubnet"
        },
        "vnet2Name": {
            "type": "string",
            "metadata": {
                "description": "description"
            },
            "defaultValue": "ManufacturingVnet"
        },
        "vnet2subnetOneName": {
            "type": "string",
            "metadata": {
                "description": "description"
            },
            "defaultValue": "sensorSubnet1"
        },
        "vnet2subnetTwoName": {
            "type": "string",
            "metadata": {
                "description": "description"
            },
            "defaultValue": "sensorSubnet2"
        },
        "vnet1SubnetNSG": {
            "type": "string",
            "metadata": {
                "description": "description"
            },
            "defaultValue": "SharedServicesSubnetNSG"
        }
    },
    "variables": {
        "vnet1AddressPrefix": "10.20.0.0/16",
        "vnet1subnetOneNameAddress": "10.20.10.0/24",
        "vnet1subnetTwoNameAddress": "10.20.20.0/24",
        "vnet2AddressPrefix": "10.30.0.0/16",
        "vnet2subnetOneNameAddress": "10.30.20.0/24",
        "vnet2subnetTwoNameAddress": "10.30.21.0/24"
    },
    "resources": [
        {
            "name": "[parameters('vnet1Name')]",
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2024-03-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "virtualNetwork1"
            },
            "dependsOn": [],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('vnet1AddressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[parameters('vnet1subnetOneName')]",
                        "properties": {
                            "addressPrefix": "[variables('vnet1subnetOneNameAddress')]",
                            "networkSecurityGroup": {
                                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('vnet1SubnetNSG'))]"
                            }
                        }
                    },
                    {
                        "name": "[parameters('vnet1subnetTwoName')]",
                        "properties": {
                            "addressPrefix": "[variables('vnet1subnetTwoNameAddress')]",
                            "networkSecurityGroup": {
                                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('vnet1SubnetNSG'))]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "name": "[parameters('vnet2Name')]",
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2024-03-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "virtualNetwork2"
            },
            "dependsOn": [],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('vnet2AddressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[parameters('vnet2subnetOneName')]",
                        "properties": {
                            "addressPrefix": "[variables('vnet2subnetOneNameAddress')]"
                        }
                    },
                    {
                        "name": "[parameters('vnet2subnetTwoName')]",
                        "properties": {
                            "addressPrefix": "[variables('vnet2subnetTwoNameAddress')]"
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {}
}

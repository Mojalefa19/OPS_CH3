{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vnet1Name": {
        "type": "string",
        "metadata": {
          "description": "Name of the Core Services Virtual Network.",
          "displayName": "CoreServicesVnet"
        },
        "defaultValue": "CoreServicesVnet"
      },
      "vnet1subnetOneName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Shared Services Subnet.",
          "displayName": "SharedServiceSubnet"
        },
        "defaultValue": "SharedServicesSubnet"
      },
      "vnet1subnetTwoName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Database Subnet.",
          "displayName": "DatabaseSubnet"
        },
        "defaultValue": "DatabaseSubnet"
      },
      "vnet2Name": {
        "type": "string",
        "metadata": {
          "description": "Name of the Manufacturing Virtual Network."
        },
        "defaultValue": "ManufacturingVnet"
      },
      "vnet2subnetOneName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Sensor Subnet 1."
        },
        "defaultValue": "SensorSubnet1"
      },
      "vnet2subnetTwoName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Sensor Subnet 2."
        },
        "defaultValue": "SensorSubnet2"
      },
      "vnet1SubnetNSG": {
        "type": "string",
        "metadata": {
          "description": "Name of the Network Security Group for CoreServicesVnet subnets."
        },
        "defaultValue": "myNSGSecure"
      },
      "vnet2SubnetNSG": {
        "type": "string",
        "metadata": {
          "description": "Name of the Network Security Group for ManufacturingVnet subnets."
        },
        "defaultValue": "ManufacturingSubnetNSG"
      },
      "vnet1AddressPrefix": {
        "type": "string",
        "defaultValue": "10.20.0.0/16"
      },
      "vnet2AddressPrefix": {
        "type": "string",
        "defaultValue": "10.30.0.0/16"
      },
      "vnet1subnetOneAddress": {
        "type": "string",
        "defaultValue": "10.20.10.0/24"
      },
      "vnet1subnetTwoAddress": {
        "type": "string",
        "defaultValue": "10.20.20.0/24"
      },
      "vnet2subnetOneAddress": {
        "type": "string",
        "defaultValue": "10.30.20.0/24"
      },
      "vnet2subnetTwoAddress": {
        "type": "string",
        "defaultValue": "10.30.21.0/24"
      },
      "asgName":{
        "type": "string",
        "defaultValue": "asg-web"
      },
      "publicDnsZoneName":{
        "type": "string",
        "defaultValue": "contoso.com"
      },
      "privateDnsZoneName":{
        "type": "string",
        "defaultValue": "private.contoso.com"
      }
  
    },
    "variables": {},
    "resources": [
      {
        "name": "[parameters('vnet1Name')]",
        "type": "Microsoft.Network/virtualNetworks",
        "apiVersion": "2024-03-01",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "CoreServicesVnet"
        },
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "[parameters('vnet1AddressPrefix')]"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('vnet1subnetOneName')]",
              "properties": {
                "addressPrefix": "[parameters('vnet1subnetOneAddress')]",
                "networkSecurityGroup": {
                  "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('vnet1SubnetNSG'))]"
                }
              }
            },
            {
              "name": "[parameters('vnet1subnetTwoName')]",
              "properties": {
                "addressPrefix": "[parameters('vnet1subnetTwoAddress')]",
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
          "displayName": "ManufacturingVnet"
        },
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "[parameters('vnet2AddressPrefix')]"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('vnet2subnetOneName')]",
              "properties": {
                "addressPrefix": "[parameters('vnet2subnetOneAddress')]",
                "networkSecurityGroup": {
                  "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('vnet2SubnetNSG'))]"
                }
              }
            },
            {
              "name": "[parameters('vnet2subnetTwoName')]",
              "properties": {
                "addressPrefix": "[parameters('vnet2subnetTwoAddress')]",
                "networkSecurityGroup": {
                  "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('vnet2SubnetNSG'))]"
                }
              }
            }
          ]
        }
      },
      {
        "type": "Microsoft.Network/applicationSecurityGroups",
        "apiVersion": "2024-03-01",
        "name": "[parameters('asgName')]",
        "location": "[resourceGroup().location]",
        "properties": {}
      },
      {
        "type": "Microsoft.Network/networkSecurityGroups",
        "apiVersion": "2024-03-01",
        "name": "[parameters('vnet1SubnetNSG')]",
        "location": "[resourceGroup().location]",
        "properties": {
          "securityRules": [
            {
              "name": "AllowASG",
              "properties": {
                "priority": 100,
                "access": "Allow",
                "direction": "Inbound",
                "sourceAddressPrefix": "*",
                "sourcePortRange": "*",
                "destinationAddressPrefix": "*",
                "destinationPortRanges": [
                  "80",
                  "443"
                ],
                "protocol": "Tcp",
                "sourceApplicationSecurityGroups": [
                  {
                    "id": "[resourceId('Microsoft.Network/applicationSecurityGroups', parameters('asgName'))]"
                  }
                ]
              }
            },
            {
              "name": "DenyAnyCustom8080Outbound",
              "properties": {
                "priority": 4096,
                "access": "Deny",
                "direction": "Outbound",
                "sourceAddressPrefix": "*",
                "sourcePortRange": "*",
                "destinationAddressPrefix": "Internet",
                "destinationPortRanges": [
                  "8080"
                ],
                "protocol": "Any"
              }
            }
          ]
        }
      },
          {
              "type": "Microsoft.Network/dnsZones",
              "apiVersion": "2018-05-01",
              "name": "[parameters('publicDnsZoneName')]",
              "location": "global",
              "properties": {}
          },
          {
              "type": "Microsoft.Network/privateDnsZones",
              "apiVersion": "2018-09-01",
              "name": "[parameters('privateDnsZoneName')]",
              "location": "global",
              "properties": {}
          },
                  {
              "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
              "apiVersion": "2018-09-01",
              "name": "[concat(parameters('privateDnsZoneName'), '/manufacturing-link')]",
              "location": "global",
              "properties": {
                  "registrationEnabled": false,
                  "virtualNetwork": {
                      "id": "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnet2Name'))]"
                  }
              }
          }
    ],
    "outputs": {}
  }

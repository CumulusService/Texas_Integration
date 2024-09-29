{
    "definition": {
        "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
        "actions": {
            "CheckErr": {
                "actions": {
                    "FormXML": {
                        "inputs": "@xml(outputs('StructureRetInput'))",
                        "runAfter": {
                            "StructureRetInput": [
                                "SUCCEEDED"
                            ]
                        },
                        "type": "Compose"
                    },
                    "RetrievePickOrderFreight": {
                        "inputs": {
                            "body": "@outputs('FormXML')",
                            "headers": {
                                "Content-Type": "text/xml; charset=utf-8",
                                "SOAPAction": "@{variables('Resource')}"
                            },
                            "method": "POST",
                            "uri": "http://77.243.35.82:5324/LOGIAERPInterface/"
                        },
                        "runAfter": {
                            "FormXML": [
                                "SUCCEEDED"
                            ]
                        },
                        "runtimeConfiguration": {
                            "contentTransfer": {
                                "transferMode": "Chunked"
                            }
                        },
                        "type": "Http"
                    },
                    "SetRetrieveOrderFreight": {
                        "inputs": {
                            "name": "Resource",
                            "value": "http://www.logiawms.dk/IERP/DeletePickingOrder"
                        },
                        "type": "SetVariable"
                    },
                    "StructureRetInput": {
                        "inputs": "<soapenv:Envelope xmlns:log1=\"http://schemas.datacontract.org/2004/07/Logimatic.Interface.Plugin.Models.ERP\" xmlns:log=\"http://www.logiawms.dk/\" xmlns:soapenv=\"http://schemas.xmlsoap.org/soap/envelope/\">\n\t<soapenv:Header/>\n\t<soapenv:Body>\n\t\t<log:DeletePickingOrder>\n\t\t\t<log:pickingOrderKey>\n\t\t\t\t<log1:OrderNumber1>@{triggerOutputs()?['headers']?['DocNum']}</log1:OrderNumber1>                                                \n\t\t\t</log:pickingOrderKey>\n\t\t</log:DeletePickingOrder>\n\t</soapenv:Body>\n</soapenv:Envelope>",
                        "runAfter": {
                            "SetRetrieveOrderFreight": [
                                "SUCCEEDED"
                            ]
                        },
                        "type": "Compose"
                    }
                },
                "else": {
                    "actions": {}
                },
                "expression": {
                    "and": [
                        {
                            "equals": [
                                "@triggerOutputs()?['headers']?['status']",
                                "ERROR"
                            ]
                        }
                    ]
                },
                "runAfter": {
                    "Resource": [
                        "SUCCEEDED"
                    ]
                },
                "type": "If"
            },
            "Resource": {
                "inputs": {
                    "variables": [
                        {
                            "name": "Resource",
                            "type": "string"
                        }
                    ]
                },
                "runAfter": {},
                "type": "InitializeVariable"
            }
        },
        "contentVersion": "1.0.0.0",
        "outputs": {},
        "triggers": {
            "When_a_HTTP_request_is_received": {
                "kind": "Http",
                "type": "Request"
            }
        }
    },
    "kind": "Stateful"
}

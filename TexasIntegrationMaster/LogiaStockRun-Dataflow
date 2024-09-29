{
    "definition": {
        "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
        "actions": {
            "BuildCompleteArray": {
                "inputs": "@union(variables('varFinalArray'),outputs('SelectStdArr')?['body'])",
                "runAfter": {
                    "SerialsArray": [
                        "SUCCEEDED"
                    ]
                },
                "type": "Compose"
            },
            "CheckFlowResultStatus": {
                "actions": {
                    "Condition": {
                        "actions": {},
                        "else": {
                            "actions": {
                                "Send_an_email_(V2)_1": {
                                    "inputs": {
                                        "body": {
                                            "Bcc": "mosn@bitpeople.com;laki@bitpeople.com",
                                            "Body": "<p>@{variables('varHTML')}</p><p>Inventory Document Generation was skipped as no eligilbe items were found.</p>",
                                            "Importance": "Normal",
                                            "Subject": "Inventory Document Generation ",
                                            "To": "sma@texas.dk;tb@texas.dk"
                                        },
                                        "host": {
                                            "connection": {
                                                "referenceName": "office365-2"
                                            }
                                        },
                                        "method": "post",
                                        "path": "/v2/Mail"
                                    },
                                    "type": "ApiConnection"
                                },
                                "Terminate": {
                                    "inputs": {
                                        "runStatus": "Cancelled"
                                    },
                                    "runAfter": {
                                        "Send_an_email_(V2)_1": [
                                            "SUCCEEDED"
                                        ]
                                    },
                                    "type": "Terminate"
                                }
                            }
                        },
                        "expression": {
                            "and": [
                                {
                                    "greater": [
                                        "@length(body('List_rows_(preview)')?['value'])",
                                        0
                                    ]
                                }
                            ]
                        },
                        "runAfter": {
                            "List_rows_(preview)": [
                                "SUCCEEDED"
                            ]
                        },
                        "type": "If"
                    },
                    "List_rows_(preview)": {
                        "inputs": {
                            "headers": {
                                "accept": "application/json;odata.metadata=full",
                                "organization": "https://org126ca510.crm4.dynamics.com",
                                "prefer": "odata.include-annotations=*"
                            },
                            "host": {
                                "connection": {
                                    "referenceName": "commondataservice"
                                }
                            },
                            "method": "get",
                            "path": "/api/data/v9.1/@{encodeURIComponent(encodeURIComponent('cr362_logiainventoryfinaloutputs'))}"
                        },
                        "type": "ApiConnection"
                    }
                },
                "else": {
                    "actions": {
                        "Send_an_email_(V2)_1_copy": {
                            "inputs": {
                                "body": {
                                    "Bcc": "mosn@bitpeople.com;laki@bitpeople.com",
                                    "Body": "<p>@{variables('varHTML')}</p><p>Inventory Document Generation was cancelled due to a runtime error.</p>",
                                    "Importance": "Normal",
                                    "Subject": "Inventory Document Generation ",
                                    "To": "sma@texas.dk;tb@texas.dk"
                                },
                                "host": {
                                    "connection": {
                                        "referenceName": "office365-2"
                                    }
                                },
                                "method": "post",
                                "path": "/v2/Mail"
                            },
                            "type": "ApiConnection"
                        },
                        "TerminateFlow": {
                            "inputs": {
                                "runStatus": "Cancelled"
                            },
                            "runAfter": {
                                "Send_an_email_(V2)_1_copy": [
                                    "SUCCEEDED"
                                ]
                            },
                            "type": "Terminate"
                        }
                    }
                },
                "expression": {
                    "and": [
                        {
                            "equals": [
                                "@outputs('When_a_dataflow_refresh_completes')?['body']?['status']",
                                "Success"
                            ]
                        }
                    ]
                },
                "runAfter": {
                    "When_a_dataflow_refresh_completes": [
                        "SUCCEEDED"
                    ]
                },
                "type": "If"
            },
            "ComposeOutput": {
                "inputs": {
                    "InventoryPostingLines": "@outputs('BuildCompleteArray')"
                },
                "runAfter": {
                    "BuildCompleteArray": [
                        "SUCCEEDED"
                    ]
                },
                "type": "Compose"
            },
            "Condition_1": {
                "actions": {
                    "Send_an_email_(V2)": {
                        "inputs": {
                            "body": {
                                "Bcc": "mosn@bitpeople.com;laki@bitpeople.com",
                                "Body": "<p>@{variables('varHTML')}</p><p>Dear Texas Team Members,</p><p>Inventory Posting document  @{outputs('PostStock')?['body']?['DocumentNumber']} has been successfully generated.</p>",
                                "Importance": "Normal",
                                "Subject": "Inventory Document Generation ",
                                "To": "sma@texas.dk;tb@texas.dk"
                            },
                            "host": {
                                "connection": {
                                    "referenceName": "office365-2"
                                }
                            },
                            "method": "post",
                            "path": "/v2/Mail"
                        },
                        "type": "ApiConnection"
                    }
                },
                "else": {
                    "actions": {
                        "Send_an_email_(V2)_copy": {
                            "inputs": {
                                "body": {
                                    "Bcc": "mosn@bitpeople.com;laki@bitpeople.com",
                                    "Body": "<p>@{variables('varHTML')}</p><p>Dear Texas Team Members,</p><p>The Following error occured while trying to generate the Inventory Posting document:</p><br><p>@{outputs('PostStock')?['body']?['error']?['message']?['value']}</p>",
                                    "Importance": "Normal",
                                    "Subject": "Inventory Document Generation ",
                                    "To": "sma@texas.dk;tb@texas.dk"
                                },
                                "host": {
                                    "connection": {
                                        "referenceName": "office365-2"
                                    }
                                },
                                "method": "post",
                                "path": "/v2/Mail"
                            },
                            "type": "ApiConnection"
                        }
                    }
                },
                "expression": {
                    "and": [
                        {
                            "equals": [
                                "@outputs('PostStock')?['statusCode']",
                                201
                            ]
                        }
                    ]
                },
                "runAfter": {
                    "PostStock": [
                        "SUCCEEDED",
                        "FAILED",
                        "TIMEDOUT"
                    ]
                },
                "type": "If"
            },
            "Create_CSV_table": {
                "inputs": {
                    "format": "CSV",
                    "from": "@outputs('GetStock')?['body']?['value']"
                },
                "runAfter": {
                    "GetItems": [
                        "SUCCEEDED"
                    ]
                },
                "type": "Table"
            },
            "Create_CSV_table_1": {
                "inputs": {
                    "format": "CSV",
                    "from": "@outputs('GetItems')?['body']?['value']"
                },
                "runAfter": {
                    "Create_CSV_table": [
                        "SUCCEEDED"
                    ]
                },
                "type": "Table"
            },
            "Create_file": {
                "inputs": {
                    "body": "@body('Create_CSV_table')",
                    "host": {
                        "connection": {
                            "referenceName": "onedriveforbusiness-2"
                        }
                    },
                    "method": "post",
                    "path": "/datasets/default/files",
                    "queries": {
                        "folderPath": "/TexasLogiaInventory",
                        "name": "LogiaStock.txt"
                    }
                },
                "runAfter": {
                    "Create_CSV_table_1": [
                        "SUCCEEDED"
                    ]
                },
                "runtimeConfiguration": {
                    "contentTransfer": {
                        "transferMode": "Chunked"
                    }
                },
                "type": "ApiConnection"
            },
            "Create_file_1": {
                "inputs": {
                    "body": "@body('Create_CSV_table_1')",
                    "host": {
                        "connection": {
                            "referenceName": "onedriveforbusiness-3"
                        }
                    },
                    "method": "post",
                    "path": "/datasets/default/files",
                    "queries": {
                        "folderPath": "/TexasLogiaInventory",
                        "name": "ItemsList.txt"
                    }
                },
                "runAfter": {
                    "Create_file": [
                        "SUCCEEDED"
                    ]
                },
                "runtimeConfiguration": {
                    "contentTransfer": {
                        "transferMode": "Chunked"
                    }
                },
                "type": "ApiConnection"
            },
            "For_each": {
                "actions": {
                    "Delete_file": {
                        "inputs": {
                            "host": {
                                "connection": {
                                    "referenceName": "onedriveforbusiness-5"
                                }
                            },
                            "method": "delete",
                            "path": "/datasets/default/files/@{encodeURIComponent(encodeURIComponent(items('For_each')?['Id']))}"
                        },
                        "type": "ApiConnection"
                    }
                },
                "foreach": "@body('List_files_in_folder')?['value']",
                "runAfter": {
                    "List_files_in_folder": [
                        "SUCCEEDED"
                    ]
                },
                "type": "foreach"
            },
            "GetArray": {
                "inputs": "@body('List_rows_(preview)')?['value']",
                "runAfter": {
                    "GetSerialNumbersInfo": [
                        "SUCCEEDED"
                    ]
                },
                "type": "Compose"
            },
            "GetItems": {
                "inputs": {
                    "headers": {
                        "Authorization": "Basic eyJVc2VyTmFtZSI6Im1hbmFnZXIiLCJDb21wYW55REIiOiJTYW5ka2Fzc2UyIn06VGV4U2VjdXIxdHlA",
                        "Prefer": "odata.maxpagesize=0"
                    },
                    "method": "GET",
                    "uri": "https://apsapb1sl.texas.dk:50000/b1s/v1/view.svc/GetItemCodesB1SLQuery"
                },
                "runAfter": {
                    "GetStock": [
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
            "GetSerialNumbersInfo": {
                "inputs": {
                    "headers": {
                        "Authorization": "Basic eyJVc2VyTmFtZSI6IkFUUENIIiwiQ29tcGFueURCIjoiU2FuZGthc3NlMiJ9OlRleFNlY3VyMXR5QVRQQA==",
                        "Prefer": "odata.maxpagesize=0"
                    },
                    "method": "GET",
                    "uri": "https://apsapb1sl.texas.dk:50000/b1s/v1/view.svc/GetSerialNumbersB1SLQuery"
                },
                "runAfter": {
                    "CheckFlowResultStatus": [
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
            "GetSerialsArray": {
                "inputs": {
                    "from": "@body('SelectInitialArray')",
                    "where": "@and(equals(item()?['SN'], 'Y'), equals(item()?['NegativeDiff'], 'Y'))"
                },
                "runAfter": {
                    "SelectInitialArray": [
                        "SUCCEEDED"
                    ]
                },
                "type": "Query"
            },
            "GetStandardArray": {
                "inputs": {
                    "from": "@body('SelectInitialArray')",
                    "where": "@equals(item()?['SN'],'N')"
                },
                "runAfter": {
                    "GetSerialsArray": [
                        "SUCCEEDED"
                    ]
                },
                "type": "Query"
            },
            "GetStock": {
                "inputs": {
                    "headers": {
                        "Authorization": "Basic eyJVc2VyTmFtZSI6Im1hbmFnZXIiLCJDb21wYW55REIiOiJTYW5ka2Fzc2UyIn06VGV4U2VjdXIxdHlA",
                        "Prefer": "odata.maxpagesize=0"
                    },
                    "method": "GET",
                    "uri": "https://apsapb1sl.texas.dk:50000/b1s/v1/view.svc/PullItemsB1SLQuery"
                },
                "runAfter": {
                    "varFinalArray": [
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
            "List_files_in_folder": {
                "inputs": {
                    "host": {
                        "connection": {
                            "referenceName": "onedriveforbusiness-4"
                        }
                    },
                    "method": "get",
                    "path": "/datasets/default/foldersV2/@{encodeURIComponent(encodeURIComponent('b!nNXRSTfmvEiWGF9ZziG8IUUmo4R2CTpPjjpaNC3Fh-u52oRE5qTMTLEkBmnMBZ_V.015QGSQMPIWCQBJQAK2FF373HVG4OYTWA3'))}",
                    "queries": {
                        "skipToken": "",
                        "top": 20
                    }
                },
                "metadata": {
                    "b!nNXRSTfmvEiWGF9ZziG8IUUmo4R2CTpPjjpaNC3Fh-u52oRE5qTMTLEkBmnMBZ_V.015QGSQMPIWCQBJQAK2FF373HVG4OYTWA3": "/TexasLogiaInventory"
                },
                "runAfter": {
                    "Condition_1": [
                        "SUCCEEDED"
                    ]
                },
                "type": "ApiConnection"
            },
            "PostStock": {
                "inputs": {
                    "body": "@outputs('ComposeOutput')",
                    "headers": {
                        "Authorization": "Basic eyJDb21wYW55REIiOiJTYW5ka2Fzc2UyIiwiVXNlck5hbWUiOiJCMUNVIn06QjFDbG91ZFVzZXJA"
                    },
                    "method": "POST",
                    "retryPolicy": {
                        "count": 4,
                        "interval": "PT50S",
                        "type": "fixed"
                    },
                    "uri": "https://apsapb1sl.texas.dk:50000/b1s/v1/InventoryPostings"
                },
                "runAfter": {
                    "ComposeOutput": [
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
            "Refresh_a_dataflow": {
                "inputs": {
                    "host": {
                        "connection": {
                            "referenceName": "dataflows-4"
                        }
                    },
                    "method": "post",
                    "path": "/api/groups/@{encodeURIComponent('Default-f04efc49-f464-4434-b269-2aa04006567f-31c688f59-europe')}/dataflows/@{encodeURIComponent('cc5ad6fa-9571-4ca4-8c47-0c5de48b4c10')}/refreshdataflow",
                    "queries": {
                        "workspaceType": "Environment"
                    }
                },
                "runAfter": {
                    "Create_file_1": [
                        "SUCCEEDED"
                    ]
                },
                "type": "ApiConnection"
            },
            "SelectInitialArray": {
                "inputs": {
                    "from": "@outputs('GetArray')",
                    "select": {
                        "CountedQuantity": "@{item()?['cr362_logia_phys_qty']}",
                        "ItemCode": "@{item()?['cr362_itemcode1']}",
                        "NegativeDiff": "@{item()?['cr362_negativediff']}",
                        "Price": "@{item()?['cr362_finalprice']}",
                        "SN": "@{item()?['cr362_mansernum1']}",
                        "Variance": "@{sub(item()?['cr362_logia_phys_qty'],item()?['cr362_onhand'])}",
                        "WarehouseCode": "@{item()?['cr362_warehousecode']}"
                    }
                },
                "runAfter": {
                    "GetArray": [
                        "SUCCEEDED"
                    ]
                },
                "type": "Select"
            },
            "SelectStdArr": {
                "inputs": {
                    "from": "@outputs('GetStandardArray')?['body']",
                    "select": {
                        "CountedQuantity": "@{item()?['CountedQuantity']}",
                        "ItemCode": "@{item()?['ItemCode']}",
                        "Price": "@{item()?['Price']}",
                        "WarehouseCode": "@{item()?['WarehouseCode']}"
                    }
                },
                "runAfter": {
                    "GetStandardArray": [
                        "SUCCEEDED"
                    ]
                },
                "type": "Select"
            },
            "SerialsArray": {
                "actions": {
                    "CheckLength": {
                        "actions": {
                            "AddQty": {
                                "inputs": {
                                    "from": "@outputs('SelectSNElements')",
                                    "select": {
                                        "ItemCode": "@{item()?['ItemCode']}",
                                        "Quantity": "-1",
                                        "SystemSerialNumber": "@{item()?['SysSerial']}"
                                    }
                                },
                                "runAfter": {
                                    "SelectSNElements": [
                                        "SUCCEEDED"
                                    ]
                                },
                                "type": "Select"
                            },
                            "AppendtoSNArr": {
                                "inputs": {
                                    "name": "varFinalArray",
                                    "value": "@outputs('BuildSNOutput')"
                                },
                                "runAfter": {
                                    "BuildSNOutput": [
                                        "SUCCEEDED"
                                    ]
                                },
                                "type": "AppendToArrayVariable"
                            },
                            "BuildSNOutput": {
                                "inputs": {
                                    "CountedQuantity": "@{item()?['CountedQuantity']}",
                                    "InventoryPostingSerialNumbers": "@outputs('AddQty')?['body']",
                                    "ItemCode": "@{item()?['ItemCode']}",
                                    "Price": "@{item()?['Price']}",
                                    "WarehouseCode": "@{item()?['WarehouseCode']}"
                                },
                                "runAfter": {
                                    "AddQty": [
                                        "SUCCEEDED"
                                    ]
                                },
                                "type": "Compose"
                            },
                            "SelectSNElements": {
                                "inputs": "@take(outputs('FilterSerialMaster')?['body'],int(mul(int(items('SerialsArray')?['Variance']),-1)))",
                                "type": "Compose"
                            }
                        },
                        "else": {
                            "actions": {}
                        },
                        "expression": {
                            "and": [
                                {
                                    "greaterOrEquals": [
                                        "@length(outputs('FilterSerialMaster')?['body'])",
                                        "@int(mul(int(items('SerialsArray')?['Variance']),-1))"
                                    ]
                                }
                            ]
                        },
                        "runAfter": {
                            "FilterSerialMaster": [
                                "SUCCEEDED"
                            ]
                        },
                        "type": "If"
                    },
                    "FilterSerialMaster": {
                        "inputs": {
                            "from": "@Outputs('GetSerialNumbersInfo')?['body']?['value']",
                            "where": "@and(equals(item()?['WhsCode'],items('SerialsArray')?['WarehouseCode']), equals(item()?['ItemCode'],items('SerialsArray')?['ItemCode']))"
                        },
                        "type": "Query"
                    }
                },
                "foreach": "@outputs('GetSerialsArray')?['body']",
                "runAfter": {
                    "SelectStdArr": [
                        "SUCCEEDED"
                    ]
                },
                "type": "Foreach"
            },
            "When_a_dataflow_refresh_completes": {
                "inputs": {
                    "host": {
                        "connection": {
                            "referenceName": "dataflows-3"
                        }
                    },
                    "method": "get",
                    "path": "/api/groups/@{encodeURIComponent('Default-f04efc49-f464-4434-b269-2aa04006567f-31c688f59-europe')}/dataflows/@{encodeURIComponent('cc5ad6fa-9571-4ca4-8c47-0c5de48b4c10')}/onrefreshcomplete",
                    "queries": {
                        "workspaceType": "Environment"
                    }
                },
                "runAfter": {
                    "varFinalArray": [
                        "SUCCEEDED"
                    ]
                },
                "type": "ApiConnection"
            },
            "varFinalArray": {
                "inputs": {
                    "variables": [
                        {
                            "name": "varFinalArray",
                            "type": "array",
                            "value": []
                        }
                    ]
                },
                "runAfter": {
                    "varHTML": [
                        "SUCCEEDED"
                    ]
                },
                "type": "InitializeVariable"
            },
            "varHTML": {
                "inputs": {
                    "variables": [
                        {
                            "name": "varHTML",
                            "type": "string",
                            "value": "<style>\ntable {\n  border: 1px solid #1C6EA4;\n  background-color: #EEEEEE;\n  width: 100%;\n  text-align: left;\n  border-collapse: collapse;\n}\ntable td, table th {\n  border: 1px solid #AAAAAA;\n  padding: 3px 2px;\n}\ntable tbody td {\n  font-size: 13px;\n}\ntable thead {\n  background: #1C6EA4;\n  border-bottom: 2px solid #444444;\n}\ntable thead th {\n  font-size: 15px;\n  font-weight: bold;\n  color: #FFFFFF;\n  border-left: 2px solid #D0E4F5;\n}\ntable thead th:first-child {\n  border-left: none;\n}\n</style>"
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

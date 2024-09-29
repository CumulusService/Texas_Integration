{
    "definition": {
        "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
        "actions": {
            "CheckSLCookie": {
                "actions": {
                    "ReturnExistingCookie": {
                        "inputs": {
                            "body": "@outputs('GetFileJSON')?['Cookie']",
                            "statusCode": 200
                        },
                        "kind": "Http",
                        "type": "Response"
                    }
                },
                "else": {
                    "actions": {
                        "ComposeFileInput": {
                            "inputs": {
                                "Cookie": "@{outputs('FinalCookie')}",
                                "StartTime": "@{variables('StartTime')}"
                            },
                            "runAfter": {
                                "FinalCookie": [
                                    "SUCCEEDED"
                                ]
                            },
                            "type": "Compose"
                        },
                        "Create_file": {
                            "inputs": {
                                "body": "@outputs('ComposeFileInput')",
                                "host": {
                                    "connection": {
                                        "referenceName": "onedriveforbusiness-5"
                                    }
                                },
                                "method": "post",
                                "path": "/datasets/default/files",
                                "queries": {
                                    "folderPath": "/TexasServiceLayerCookie",
                                    "name": "Cookie.json"
                                }
                            },
                            "runAfter": {
                                "ComposeFileInput": [
                                    "SUCCEEDED"
                                ]
                            },
                            "type": "ApiConnection"
                        },
                        "FinalCookie": {
                            "inputs": "@concat(outputs('GetB1SESSION'),outputs('GetROUTID'))",
                            "runAfter": {
                                "GetROUTID": [
                                    "SUCCEEDED"
                                ]
                            },
                            "type": "Compose"
                        },
                        "GetB1SESSION": {
                            "inputs": "@concat(outputs('SplitResponse')[0],';')",
                            "runAfter": {
                                "SplitResponse": [
                                    "SUCCEEDED"
                                ]
                            },
                            "type": "Compose"
                        },
                        "GetROUTID": {
                            "inputs": "@last(split(outputs('SplitResponse')[4],','))",
                            "runAfter": {
                                "GetB1SESSION": [
                                    "SUCCEEDED"
                                ]
                            },
                            "type": "Compose"
                        },
                        "LogintoSLTexas": {
                            "inputs": {
                                "body": {
                                    "CompanyDB": "Sandkasse2",
                                    "Password": "B1CloudUser@",
                                    "UserName": "B1CU"
                                },
                                "method": "POST",
                                "uri": "https://apsapb1sl.texas.dk:50000/b1s/v1/Login"
                            },
                            "runAfter": {
                                "SetTime": [
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
                        "ReturnNewCookie": {
                            "inputs": {
                                "body": "@outputs('FinalCookie')",
                                "headers": {
                                    "Connection": "Keep-Alive",
                                    "Keep-Alive": "timeout=@{Outputs('LogintoSLTexas')?['body']?['SessionTimeout']}, max=100",
                                    "Set-Cookie": "@{Outputs('LogintoSLTexas')?['headers']?['Set-Cookie']}"
                                },
                                "statusCode": 200
                            },
                            "kind": "Http",
                            "runAfter": {
                                "Create_file": [
                                    "SUCCEEDED"
                                ]
                            },
                            "type": "Response"
                        },
                        "SetTime": {
                            "inputs": {
                                "name": "StartTime",
                                "value": "@{formatDateTime(convertFromUtc(utcNow(),'Romance Standard Time'),'yyyy-MM-ddTHH:mm')}"
                            },
                            "type": "SetVariable"
                        },
                        "SplitResponse": {
                            "inputs": "@split(outputs('LogintoSLTexas')?['headers']?['Set-Cookie'],';')",
                            "runAfter": {
                                "LogintoSLTexas": [
                                    "SUCCEEDED"
                                ]
                            },
                            "type": "Compose"
                        }
                    }
                },
                "expression": {
                    "and": [
                        {
                            "less": [
                                "@formatDateTime(convertFromUtc(utcNow(),'Romance Standard Time'),'yyyy-MM-ddTHH:mm')",
                                "@addToTime(outputs('GetFileJSON')?['StartTime'],28,'Minute','yyyy-MM-ddTHH:mm')"
                            ]
                        }
                    ]
                },
                "runAfter": {
                    "GetFileJSON": [
                        "SUCCEEDED"
                    ]
                },
                "type": "If"
            },
            "GetFileJSON": {
                "inputs": "@json(base64ToString(outputs('Get_file_content')?['body']?['$content']))",
                "runAfter": {
                    "Get_file_content": [
                        "SUCCEEDED"
                    ]
                },
                "type": "Compose"
            },
            "Get_file_content": {
                "inputs": {
                    "host": {
                        "connection": {
                            "referenceName": "onedriveforbusiness-5"
                        }
                    },
                    "method": "get",
                    "path": "/datasets/default/files/@{encodeURIComponent(encodeURIComponent('b!nNXRSTfmvEiWGF9ZziG8IUUmo4R2CTpPjjpaNC3Fh-u52oRE5qTMTLEkBmnMBZ_V.015QGSQMLDU7RY2KDZRVEKAOMFU25ZW7AI'))}/content",
                    "queries": {
                        "inferContentType": true
                    }
                },
                "metadata": {
                    "b!nNXRSTfmvEiWGF9ZziG8IUUmo4R2CTpPjjpaNC3Fh-u52oRE5qTMTLEkBmnMBZ_V.015QGSQMLDU7RY2KDZRVEKAOMFU25ZW7AI": "/TexasServiceLayerCookie/Cookie.json"
                },
                "runAfter": {
                    "StartTime": [
                        "SUCCEEDED"
                    ]
                },
                "type": "ApiConnection"
            },
            "StartTime": {
                "inputs": {
                    "variables": [
                        {
                            "name": "StartTime",
                            "type": "string",
                            "value": "@{formatDateTime(convertFromUtc(utcNow(),'Romance Standard Time'),'HH:mm')}"
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

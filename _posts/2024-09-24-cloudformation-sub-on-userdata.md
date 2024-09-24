---
title: "CloudFormationのcloud-init UserData内の変数展開"
last_modified_at: 2024-09-24T00:00:00+0900
tags:
  - Linux
  - AWS
---

あるスタックから以下値がexportされているとして、
- `Test-CFn-Stack::AdminUserName`: `admin`
- `Test-CFn-Stack::TestCmd` : `uname`

以下のような記載をするCloudFormation Templateを作成すると、

```
Parameters:
 ExternalStackName:
    Description: external stack name
    Default: Test-CFn-Stuck
    AllowedValues:
      - Test-CFn-Stuck

UserData:
  Fn::Base64:
    Fn::Sub:
      - |
        #cloud-config
        users:
          - name: ${AdminUserNameDefinedInUserData}
            groups: [ wheel ]
            shell: /bin/bash
        runcmd:
          - ${TestCmdDefinedInUserData}
      - AdminUserNameDefinedInUserData: !ImportValue 
          'Fn::Sub': '${Test-CFn-Stack}::AdminUserName'
        TestCmdDefinedInUserData: !ImportValue 
          'Fn::Sub': '${Test-CFn-Stack}::TestCmd'
```

以下のようなUserDataに置換されるはず

```
UserData:
  Fn::Base64:
    Fn::Sub:
      - |
        #cloud-config
        users:
          - name: admin
            groups: [ wheel ]
            shell: /bin/bash
        runcmd:
          - uname
```
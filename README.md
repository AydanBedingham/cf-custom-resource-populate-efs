# cf-custom-resource-populate-efs
CloudFormation Template demonstrating usage of a Lambda-backed Custom Resource to create and populate an EFS Volume using a Zip File

## Overview

### 1) Cloud Formation Parameters
- EfsRootDirectory: Specific directory (eg. 'files') cannot be empty.
- EfsSubdirectory: Directory path, no slash prefix, no trailing slash, can be empty.

![Screenshot](screenshot_parameters.jpg?raw=true)



### 2) Lambda-backed Custom Resource
- Defines a lambda-backed custom resource with properies related to EfsVolume, Zipe file and destination
- Custom resource properties related to the EfsVolume, Zipe file and destination are modified this will cause the lambda-backed custom resource to re-execute the lambda function

```
  PopulateEfsCustomResource:
    Type: Custom::PopulateEfs
    Properties:
      ServiceToken: !GetAtt populateEfsFunction.Arn
      FileSystemId: !Ref FileSystem
      AccessPointId: !Ref EfsAccessPoint
      EfsRootDirectory: !Ref EfsRootDirectory
      EfsSubdirectory: !Ref EfsSubdirectory
      ZipFileUrl: !Ref ZipFileUrl
```

### 3) Lambda Function
On Create/Update:
- Downloads the zip file to the lambda's ephemeral storage (/tmp) target location
- Unzips the the zip file to the destination directory on the EFS volume
NOTE: Any files with conflicting names will be overwritten.
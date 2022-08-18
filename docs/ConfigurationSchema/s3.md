 # Schema For AWS::EC2::SecurityGroup

 ## configuration :

    
    {'description': '',
     'groupId': '',
     'groupName': '',
     'ipPermissions': [{'fromPort': '',
                        'ipProtocol': '',
                        'ipRanges': [],
                        'ipv6Ranges': [],
                        'prefixListIds': [],
                        'toPort': '',
                        'userIdGroupPairs': [{'description': '',
                                              'groupId': '',
                                              'groupName': '',
                                              'peeringStatus': '',
                                              'userId': '',
                                              'vpcId': '',
                                              'vpcPeeringConnectionId': ''}]}],
     'ipPermissionsEgress': [{'fromPort': '',
                              'ipProtocol': '',
                              'ipRanges': [''],
                              'ipv6Ranges': [],
                              'prefixListIds': [],
                              'toPort': '',
                              'userIdGroupPairs': []}],
     'ownerId': '',
     'tags': '',
     'vpcId': ''}

 ## supplementary configuration :

    
    {'ipPermissionsCidrOwners': []}

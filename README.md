Valet
=====

Control AWS instance uptime via tags with crontab scheduling

## Libraries
This is a python script so... python is required.
argparse
boto
croniter
datetime
keyring
logging

## Configuration
### Crontab
Use http://crontab.guru/ to verify your crontab syntax

E.g. `* 7-18 * * mon-fri`
indicates a server uptime of 7am to 6pm on weekdays (monday to friday)

### boto.config
```
[<profile name>]
region=<region>
aws_access_key_id=<aws_access_key_id>
keyring=<my-keyring-name>
```

Environment variable `BOTO_CONFIG` may be required for boto to read the config

```
# recommend to add into the ~/.bash_profile
export BOTO_CONFIG=<the full path of the config file>
```

Note: use additional profile for multiple region


### keyring

```
python
> import keyring
> keyring.set_password('system', <my-keyring-name>, <aws_secret_access_key>)
> 
# to view the password
> keyring.get_password('system', <my-keyring-name>)

# to delete the password
> keyring.delete_password('system', <my-keyring-name>)
```

### Execution
```
# for single or multiple profiles
python ./valet.py --profiles <list of profiles> --log /var/log/valet

--dry-run: Perform a dry run
--debug: Turn on debug logging
-l, --log: Directory where logs should be placed
-p, --profiles: Which profile(s) to check, default 'Credentials'
```

## Examples
cat boto.config

```
[profile1]
region=ap-southeast-1
aws_access_key_id=profile1accesskey
keyring=xyzxyz

[profile2]
region=app-us-east-1
aws_access_key_id=profile2accesskey
keyring=blahblahblah
```

keyring

```
keyring.set_password('system', 'xyzxyz', 'profile1secretkey')
keyring.set_password('system', 'blahblahblah', 'profile2secretkey')
```

script

```
python ./valet.py --profiles profile1 --log /var/log/valet

# or for multiple profiles
python ./valet.py --profiles profile1 profile2 --log /var/log/valet
```

## AWS Policy/ Permission

The following customized policy should be sufficient

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeInstances", 
        "ec2:DescribeImages",
        "ec2:DescribeKeyPairs", 
        "ec2:DescribeSecurityGroups",
        "ec2:DescribeAvailabilityZones",
        "ec2:RunInstances",
        "ec2:StopInstances", 
        "ec2:StartInstances"
      ],
      "Resource": "*"
    }
   ]
}
```

## Latest Change Log
- fixed a bug in parse_instances(...)
- fixed a bug which cause error when the schedule tag is modified and then left empty, and added extra step to ignore it
- added keyring
- replaced region with profiles, region is included in the boto.config now.
- update logging for more details
 

## And the credit goes to
The team from Cloudability for the original idea and source codes.

*Reference*
https://blog.cloudability.com/saved-64-devtest-instances-scheduling-uptime/

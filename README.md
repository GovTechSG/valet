Valet
=====

Control AWS instance uptime via tags with crontab scheduling.

## Libraries
This is a python script so... python is required.
argparse
boto
croniter
datetime
keyring
logging

## Configuration
boto.config (the location of this config is exposed via BOTO_CONFIG)

```
[<profile name>]
region=<region>
aws_access_key_id=<aws_access_key_id>
keyring=<my-keyring-name>
```

Environment variable `BOTO_CONFIG` is required for boto to read the config

```
# add into ~/.bash_profile
export BOTO_CONFIG=<the full path of the config file>
```

## Keyring
```
[profile1]
region=ap-southeast-1
aws_access_key_id=profile1_access_id
keyring=krxyzxyz

[profile2]
region=app-us-east-1
aws_access_key_id=profile2_access_id
keyring= krfidafdf
```

store the secret key in keyring

```
keyring.set_password('system', 'xyzxyz', 'profile1secretkey')
keyring.set_password('system', 'blahblahblah', 'profile2secretkey')
```

### Non-keyring

```
[profile1]
region=ap-southeast-1
aws_access_key_id=profile1_access_id
aws_secret_access_key=xyzxyz

[profile2]
region=app-us-east-1
aws_access_key_id=profile2_access_id
aws_secret_access_key=fidafdf
```

## Python Keyring

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

## Execution

```
# for single or multiple profiles
python ./valet.py --profiles <list of profiles> --log /var/log/valet

--dry-run: Perform a dry run
--debug: Turn on debug logging
-l, --log: Directory where logs should be placed
-p, --profiles: Which profile(s) to check, default 'Credentials'
```

Examples

```
python ./valet.py --profiles profile1 --log /var/log/valet

# or for multiple profiles
python ./valet.py --profiles profile1 profile2 --log /var/log/valet
```

via crontab, executing this command every hour at :00, this timing can be adjusted based on need.

```
0 * * * * python ./valet.py --profiles profile1 --log /var/log/valet
```

## AWS EC2

Create a custom tag `schedule` in EC2 instance page, assign uptime based on cron for the instances we want Valet to manipulate.

Empty schedule or "* * * * *" means 24/7 uptime

Assuming the valet is checking on an interval of 1 minute.

E.g.

```
* 1 * * * means the server will be up from 1:00AM to 1:59AM
0 1 * * * means the server will be up from 1:01AM
```

```
* 7-18 * * mon-fri 
means the server will be up from 7am to 6pm on weekdays (monday to friday)
```

## Miscelleneous

### AWS Policy/ Permission

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

### Cron Syntax
Use http://crontab.guru/ to verify your crontab syntax

## Latest Change Log
- fixed a bug in parse_instances(...)
- fixed a bug which cause error when the schedule tag is modified and then left empty, and added extra step to ignore it
- added keyring
- replaced region with profiles, region is included in the boto.config now.
- update logging for more details

## And the credit goes to
The team from Cloudability for the original idea and source codes.

**Reference:**
https://blog.cloudability.com/saved-64-devtest-instances-scheduling-uptime/

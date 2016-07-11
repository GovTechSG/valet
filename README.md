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
indicates a server uptime of 7am to 6pm on weekdays monday to friday

### boto.config
```
[<profile name>]
region=
aws_access_key_id=<aws_access_key_id>
keyring=<my-keyring-name>
```

### keyring
```
python
> import keyring
> keyring.set_password('system', <my-keyring-name>, <aws_secret_access_key>)
```

## Examples
```
python ./valet.py --profiles profile_name --log /var/log/valet

# for multiple profiles
python ./valet.py --profiles profile1 profile2 --log /var/log/valet

--dry-run: Perform a dry run
--debug: Turn on debug logging
-l, --log: Directory where logs should be placed
-p, --profiles: Which profile(s) to check, default 'Credentials'
```

## Latest Change Log
- fixed a bug in parse_instances(...)
- add keyring support

## TODO
- add support for multiple profiles

## And the credit goes to
Cloudability Team for the original idea and script.

*Reference*
https://blog.cloudability.com/saved-64-devtest-instances-scheduling-uptime/

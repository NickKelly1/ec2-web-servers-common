## IP Address

### Finding yur own ip adress

```bash
# https://www.cyberciti.biz/faq/how-to-find-public-ip-address-aws-ec2-or-lightsail-vm/

dig +short myip.opendns.com @resolver1.opendns.com
#  3.25.174.224

dig TXT +short o-o.myaddr.l.google.com @ns1.google.com                                                                                     <<<
# "3.25.174.224"

curl http://checkip.amazonaws.com
# 3.25.174.224
```

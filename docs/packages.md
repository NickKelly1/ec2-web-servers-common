## Packages

### Installing / removing amazon-linux-extras packages

[Usage](https://docs.amazonaws.cn/en_us/AWSEC2/latest/UserGuide/amazon-linux-ami-basics.html)

Listing:

```bash
amazon-linux-extras list
```

Installing:

```bash
sudo amazon-linux-extras install <topic>
```

Removing:

```bash
sudo yum remove $(yum list installed | grep amzn2extra-<topic> | awk '{ print $q }')
```


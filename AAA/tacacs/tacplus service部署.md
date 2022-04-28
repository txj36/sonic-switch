# tacplus Service部署

## 安装

```
sudo apt-get install tacacs+
```

## 配置

主要包括以下几个部分：
- Accounting
- Secret Key
- Groups
- Users

## 配置文件说明

man tac_plus.conf

## 具体配置

```t
# Created by Henry-Nicolas Tourneur(henry.nicolas@tourneur.be)
# See man(5) tac_plus.conf for more details
​
# Define where to log accounting data, this is the default.
​
accounting file = /var/log/tac_plus.acct
​
# This is the key that clients have to use to access Tacacs+
​
key = testing123
​
# Use /etc/passwd file to do authentication
​
#default authentication = file /etc/passwd
​
​
# You can use feature like per host key with different enable passwords
#host = 127.0.0.1 {
#        key = test
#        type = cisco
#        enable = <des|cleartext> enablepass
#        prompt = "Welcome XXX ISP Access Router \n\nUsername:"
#}
​
# We also can define local users and specify a file where data is stored.
# That file may be filled using tac_pwd
user = sonicadmin {
    name = "sonic admin"
    member = admin
    # root
    login = des bj32O1dVzaSNU
    #login = cleartext admin
    #enable = cleartext admin
    enable = des bj32O1dVzaSNU
}
​
user = test {
    name = "Test User"
    member = read-only
    login =  des 8Q9GvHI6MB7Fo
}
​
# Global enable level 15 password
user = $enab15$ {
    login = cleartext admin
}
​
# We can also specify rules valid per group of users.
#group = group1 {
# cmd = conf {
#   deny
# }
#}
group = admin {
    default service = permit
    service = exec {
        priv-lvl = 15
        }
    }
​
group = read-only {
    service = exec {
        priv-lvl = 15
        }
    cmd = show {
        permit .*
        }
    cmd = write {
        permit term
        }
    cmd = dir {
        permit .*
        }
    cmd = admin {
        permit .*
        }
    cmd = terminal {
        permit .*
        }
    cmd = more {
        permit .*
        }
    cmd = exit {
        permit .*
        }
    cmd = logout {
        permit .*
        }
}
​
​
# Another example : forbid configure command for some hosts
# for a define range of clients
#group = group1 {
# login = PAM
# service = ppp
# protocol = ip {
#   addr = 10.10.0.0/24
# }
# cmd = conf {
#   deny .*
# }
#}
​
user = DEFAULT {
  login = PAM
  service = ppp protocol = ip {}
}
​
# Much more features are availables, like ACL, more service compatibilities,
# commands authorization, scripting authorization.
# See the man page for those features.
```

> user配置中的login = des xxxx为登陆密码的加密密文，通过tac_pwd生成

```
~$ tac_pwd
Password to be encrypted: user123
lFMpH5LBpwwHA
```

## 服务命令

```
service tacacs_plus check|status|stop|start|restart
```

- 参考
  - [How to configure tac_plus (TACACS+ daemon) on Ubuntu Server](https://networkjutsu.com/tacacs-ubuntu/)
  - [Ubuntu Manpage:tac_plus.conf - tacacs+ daemon configuration file](http://manpages.ubuntu.com/manpages/xenial/man5/tac_plus.conf.5.html#example%20tac_plus%20configuration)
  - [Configuring TACACS+ Server on Ubuntu 14.04LTS &#8211; Keeran&#039;s Blog](https://blog.marquis.co/configuring-tacacs-server-on-ubuntu-14-04lts/)


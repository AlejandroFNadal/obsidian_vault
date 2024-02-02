Forward X11 through a non forwarding machine
$ ssh -X -oProxyCommand="ssh userA@host.address.A -W %h:%p" userB@host.address.B
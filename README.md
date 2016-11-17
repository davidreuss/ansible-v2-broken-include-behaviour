Simplest possible reproduction of broken include behaviour, in a non-role based environment.

```
.
├── README.md
├── playbook.yml
└── site
    ├── plays
    │   └── a
    │       └── play.yml
    ├── tasks
    │   └── task.yml
    └── templates
        └── tpl.j2
```

Running:

`$ ansible-playbook -c local -i localhost, playbook.yml`

Expected to just work, like it does with 1.8.x (and possibly, 1.9.x?)

Broken output:

```
$ ansible-playbook -c local -i localhost, playbook.yml -vvvvv
No config file found; using defaults
Set default localhost to localhost
statically included: /Users/david/Projects/ansible-v2-broken-include-behaviour/site/tasks/task.yml
Loading callback plugin default of type stdout, v2.0 from /Users/david/.pco/venv/src/ansible/lib/ansible/plugins/callback/__init__.pyc

PLAYBOOK: playbook.yml *********************************************************
2 plays in playbook.yml

PLAY [all] *********************************************************************

TASK [setup] *******************************************************************
Using module file /Users/david/.pco/venv/src/ansible/lib/ansible/modules/core/system/setup.py
<localhost> ESTABLISH LOCAL CONNECTION FOR USER: david
<localhost> EXEC /bin/sh -c '( umask 77 && mkdir -p "` echo $HOME/.ansible/tmp/ansible-tmp-1479368714.21-226192220733033 `" && echo ansible-tmp-1479368714.21-226192220733033="` echo $HOME/.ansible/tmp/ansible-tmp-1479368714.21-226192220733033 `" ) && sleep 0'
<localhost> PUT /var/folders/kv/k0w5yp093v360symx6pwpsx40000gn/T/tmpMXA0c_ TO /Users/david/.ansible/tmp/ansible-tmp-1479368714.21-226192220733033/setup.py
<localhost> EXEC /bin/sh -c 'chmod u+x /Users/david/.ansible/tmp/ansible-tmp-1479368714.21-226192220733033/ /Users/david/.ansible/tmp/ansible-tmp-1479368714.21-226192220733033/setup.py && sleep 0'
<localhost> EXEC /bin/sh -c '/usr/bin/python /Users/david/.ansible/tmp/ansible-tmp-1479368714.21-226192220733033/setup.py; rm -rf "/Users/david/.ansible/tmp/ansible-tmp-1479368714.21-226192220733033/" > /dev/null 2>&1 && sleep 0'
ok: [localhost]

PLAY [all] *********************************************************************

TASK [setup] *******************************************************************
Using module file /Users/david/.pco/venv/src/ansible/lib/ansible/modules/core/system/setup.py
<localhost> ESTABLISH LOCAL CONNECTION FOR USER: david
<localhost> EXEC /bin/sh -c '( umask 77 && mkdir -p "` echo $HOME/.ansible/tmp/ansible-tmp-1479368714.67-214413814266131 `" && echo ansible-tmp-1479368714.67-214413814266131="` echo $HOME/.ansible/tmp/ansible-tmp-1479368714.67-214413814266131 `" ) && sleep 0'
<localhost> PUT /var/folders/kv/k0w5yp093v360symx6pwpsx40000gn/T/tmpOBdDX0 TO /Users/david/.ansible/tmp/ansible-tmp-1479368714.67-214413814266131/setup.py
<localhost> EXEC /bin/sh -c 'chmod u+x /Users/david/.ansible/tmp/ansible-tmp-1479368714.67-214413814266131/ /Users/david/.ansible/tmp/ansible-tmp-1479368714.67-214413814266131/setup.py && sleep 0'
<localhost> EXEC /bin/sh -c '/usr/bin/python /Users/david/.ansible/tmp/ansible-tmp-1479368714.67-214413814266131/setup.py; rm -rf "/Users/david/.ansible/tmp/ansible-tmp-1479368714.67-214413814266131/" > /dev/null 2>&1 && sleep 0'
ok: [localhost]

TASK [template] ****************************************************************
task path: /Users/david/Projects/ansible-v2-broken-include-behaviour/site/tasks/task.yml:3
looking for "../templates/tpl.j2" at "/Users/david/Projects/ansible-v2-broken-include-behaviour/site/plays/a/templates/../templates/tpl.j2"
looking for "../templates/tpl.j2" at "/Users/david/Projects/ansible-v2-broken-include-behaviour/site/plays/a/../templates/tpl.j2"
fatal: [localhost]: FAILED! => {
    "changed": false,
    "failed": true,
    "invocation": {
        "module_args": {
            "dest": "/tmp/expand-tpl.txt",
            "src": "../templates/tpl.j2"
        },
        "module_name": "template"
    },
    "msg": "Unable to find '../templates/tpl.j2' in expected paths."
}
    to retry, use: --limit @/Users/david/Projects/ansible-v2-broken-include-behaviour/playbook.retry

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=1
```

Working: (1.8.x)

```
$ ansible-playbook -c local -i localhost, playbook.yml -vvvvv

PLAY [all] ********************************************************************

GATHERING FACTS ***************************************************************
<localhost> REMOTE_MODULE setup
<localhost> EXEC ['/bin/sh', '-c', 'mkdir -p $HOME/.ansible/tmp/ansible-tmp-1479368771.4-108305989997610 && chmod a+rx $HOME/.ansible/tmp/ansible-tmp-1479368771.4-108305989997610 && echo $HOME/.ansible/tmp/ansible-tmp-1479368771.4-108305989997610']
<localhost> PUT /var/folders/kv/k0w5yp093v360symx6pwpsx40000gn/T/tmpz3bM_t TO /Users/david/.ansible/tmp/ansible-tmp-1479368771.4-108305989997610/setup
<localhost> EXEC ['/bin/sh', '-c', u'LANG=en_US.UTF-8 LC_CTYPE=en_US.UTF-8 /usr/bin/python /Users/david/.ansible/tmp/ansible-tmp-1479368771.4-108305989997610/setup; rm -rf /Users/david/.ansible/tmp/ansible-tmp-1479368771.4-108305989997610/ >/dev/null 2>&1']
ok: [localhost]

PLAY [all] ********************************************************************

GATHERING FACTS ***************************************************************
<localhost> REMOTE_MODULE setup
<localhost> EXEC ['/bin/sh', '-c', 'mkdir -p $HOME/.ansible/tmp/ansible-tmp-1479368772.46-98778226314015 && chmod a+rx $HOME/.ansible/tmp/ansible-tmp-1479368772.46-98778226314015 && echo $HOME/.ansible/tmp/ansible-tmp-1479368772.46-98778226314015']
<localhost> PUT /var/folders/kv/k0w5yp093v360symx6pwpsx40000gn/T/tmp8etfk3 TO /Users/david/.ansible/tmp/ansible-tmp-1479368772.46-98778226314015/setup
<localhost> EXEC ['/bin/sh', '-c', u'LANG=en_US.UTF-8 LC_CTYPE=en_US.UTF-8 /usr/bin/python /Users/david/.ansible/tmp/ansible-tmp-1479368772.46-98778226314015/setup; rm -rf /Users/david/.ansible/tmp/ansible-tmp-1479368772.46-98778226314015/ >/dev/null 2>&1']
ok: [localhost]

TASK: [template src=../templates/tpl.j2 dest=/tmp/expand-tpl.txt] *************
<localhost> EXEC ['/bin/sh', '-c', 'mkdir -p $HOME/.ansible/tmp/ansible-tmp-1479368772.71-105853305773850 && chmod a+rx $HOME/.ansible/tmp/ansible-tmp-1479368772.71-105853305773850 && echo $HOME/.ansible/tmp/ansible-tmp-1479368772.71-105853305773850']
<localhost> EXEC ['/bin/sh', '-c', u'rc=flag; [ -r "/tmp/expand-tpl.txt" ] || rc=2; [ -f "/tmp/expand-tpl.txt" ] || rc=1; [ -d "/tmp/expand-tpl.txt" ] && rc=3; python -V 2>/dev/null || rc=4; [ x"$rc" != "xflag" ] && echo "${rc} /tmp/expand-tpl.txt" && exit 0; (python -c \'import hashlib; print(hashlib.sha1(open("/tmp/expand-tpl.txt", "rb").read()).hexdigest())\' 2>/dev/null) || (python -c \'import sha; print(sha.sha(open("/tmp/expand-tpl.txt", "rb").read()).hexdigest())\' 2>/dev/null) || (echo "0 /tmp/expand-tpl.txt")']
<localhost> PUT /var/folders/kv/k0w5yp093v360symx6pwpsx40000gn/T/tmpG7ib46 TO /Users/david/.ansible/tmp/ansible-tmp-1479368772.71-105853305773850/file
<localhost> EXEC ['/bin/sh', '-c', u'LANG=en_US.UTF-8 LC_CTYPE=en_US.UTF-8 /usr/bin/python /Users/david/.ansible/tmp/ansible-tmp-1479368772.71-105853305773850/file; rm -rf /Users/david/.ansible/tmp/ansible-tmp-1479368772.71-105853305773850/ >/dev/null 2>&1']
ok: [localhost] => {"changed": false, "gid": 20, "group": "staff", "mode": "0644", "owner": "david", "path": "/tmp/expand-tpl.txt", "size": 64, "state": "file", "uid": 501}

PLAY RECAP ********************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0
```

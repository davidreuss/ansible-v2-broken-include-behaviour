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

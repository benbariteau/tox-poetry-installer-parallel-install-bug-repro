This repo contains a minimal reproduction for a particular issue when using `tox-poetry-installer`.
When `skipsdist = true` is set, then any packages with missing and unbuildable wheels will silently be missing from the built virtualenv.

This repo makes a simple reproduction of this issue by installing `greenlet@1.0.0`, which has a wheel for python 3.9 but not python 3.10.
When `tox` is run, it will appear to succeed, but the diffence can be seen by introspecting the virtualenvs like so:

```
$ .tox/py39/bin/pip freeze
greenlet==1.0.0
$ .tox/py310/bin/pip freeze
$
```

Removing `skipsdist = true` fixes the problem, causing `tox` to show an error message and return 1 for its exitcode.

However, one other curious thing is that passing `--parallel-install-threads 0` to `tox` partially fixes the issue.
In this case, `tox` still claims a successful build, but afterwards prints out an error traceback and its exitcode is 1.

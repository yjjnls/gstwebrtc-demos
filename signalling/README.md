## Overview

Read Protocol.md

## Dependencies

* Python 3
* py -3 -m pip install --user websockets

## Example usage

In three separate tabs, run consecutively:
```
$ generate_certs.bat
```
**key.pem** is the **private key** and **cert.pem** is the **certificate**.


```console

$ py -3 simple-server.py
```

```console
$ py -3 session-client.py
Our uid is 'ws-test-client-8f63b9'
```

start another client to connect to session-id ws-test-client-8f63b9
```console
$ py -3 session-client.py --call ws-test-client-8f63b9
```

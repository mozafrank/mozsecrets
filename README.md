# mozsecrets

mozsecrets is a thin layer for cloud vendor secrets managers which asserts some consistency between various solutions and makes implement multi-cloud friendly code easy.

The mozsecrets library includes a cli mozilla-secrets which exposes all the features of the library.

Current supported platforms include GCP (secretmanager) and AWS (secrets manager).

## How to Install

From the command line:
```
pip3 install git+https://github.com/mozafrank/mozsecrets.git
```
From Pipenv:
```
[packages]
mozsecrets = { version = "*", git = "https://github.com/mozafrank/mozsecrets.git", ref = "master", editable = false }
```
If you're using pipenv to generate a requirements file for pypi try this syntax:
```
pipenv install
pipenv run pip freeze | grep -v pkg-resources | sed -e 's|^-e ||g' -e 's|\(.*\)egg=\(.*\)|\2@\1egg=\2|g' > requirements.txt
pip3 install --upgrade --no-cache-dir .
```

## How to Use the library

For AWS and GCP the library assumes you're fully authenticated and authorized for secrets management. For GCP, you will need to let the library know what project you want to use. This can be done with the PROJECT env var or by passing the `project` argument to the class.

```
>>> from mozsecrets.gcp import Secrets
>>> mySecrets = Secrets("afrank-secrets")
>>> dict(mySecrets)
{'THIS': 'is a secret', 'ANOTHER': 'SECRET2'}
>>> mySecrets.set('another','secret')
'projects/357203911999/secrets/afrank-secrets/versions/5'
>>> dict(mySecrets)
{'THIS': 'is a secret', 'ANOTHER': 'SECRET2', 'another': 'secret'}

```

## How to Use the CLI

```
usage: mozilla-secrets [-h] [-E] [-D] [-X] [-p PROVIDER] -s SECRET [-k KEY]
                       [-f FILE] [-v VALUE] [-b B64VALUE] [-g GCPPROJECT]

Mozilla-IT Secrets

optional arguments:
  -h, --help            show this help message and exit
  -E, --encrypt         Encrypt. Cannot be used with -D or -X
  -D, --decrypt         Decrypt. Cannot be used with -E or -X
  -X, --delete          Delete Secret (or key if -k is specified). Cannot be
                        used with -E or -D
  -p PROVIDER, --provider PROVIDER
                        Provider. Supported: AWS|GCP
  -s SECRET, --secret SECRET
                        which secret resource to work with
  -k KEY, --key KEY     which key inside a secret to work with. if no key is
                        specified you are working on the whole secret
  -f FILE, --file FILE  file to use for input {encryption} or output
                        {decryption}
  -v VALUE, --value VALUE
                        value to use for input {encryption}. Note: this
                        argument takes precedence over -f
  -b B64VALUE, --b64value B64VALUE
                        base64-encoded value to use for input {encryption}.
                        Note: this argument takes precedence over -v
  -g GCPPROJECT, --gcpproject GCPPROJECT
                        if using the GCP secret manager you must specify the
                        project you want to use
```
Create and retrieve a secret key:
```
$ mozilla-secrets -E -p GCP -g dp2-stage -s afrank-secrets -k YETANOTHER -v SECRETVALUE

$ mozilla-secrets -D -p GCP -g dp2-stage -s afrank-secrets -k YETANOTHER
SECRETVALUE
```
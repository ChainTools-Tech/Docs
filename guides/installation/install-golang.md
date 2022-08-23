# Install Golang

### Download and extract repository

#### AMD and Intel compatible processors

```bash
GOVER=$(curl https://go.dev/VERSION?m=text)
wget https://golang.org/dl/${GOVER}.linux-amd64.tar.gz
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf ${GOVER}.linux-amd64.tar.gz
```

#### **ARM processors**

```bash
GOVER=$(curl https://go.dev/VERSION?m=text)
wget https://golang.org/dl/${GOVER}.linux-arm64.tar.gz
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf ${GOVER}.linux-arm64.tar.gz
```

**NOTE**: That will install latest version of Go

### Add Go environmental variables

Set of variables, which should be set for user(s) with need to build Go apps.

That can be placed in `${HOME}/.profile` or `${HOME}/.bashrc` or any other shell-specific file, whic sets variable during logon

```bash
# add environmental variables for Go
if [ -f "/usr/local/go/bin/go" ] ; then
    export GOROOT=/usr/local/go
    export GOPATH=${HOME}/go
    export GOBIN=$GOPATH/bin
    export PATH=${PATH}:${GOROOT}/bin:${GOBIN}
fi
```

**NOTE:** To make sure that Go-specific environment will be added to new users profiles below code needs to be added to `/etc/skel/.profile`.

### Important

Once all changes are applied and files installed make sure all shell instances will be closed and then logoff and logon again to system.

That way all environmental variables will be set correctly.

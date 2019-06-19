# tokget

[![GoDoc][doc-img]][doc] [![Build Status][build-img]][build] [![codecov][codecov-img]][codecov] [![Go Report Card][goreport-img]][goreport]

`tokget` is a CLI tool that allows to get a user's access token and ID token by [the OpenID Connect protocol][oidc-spec-core].

**Features**

- authenticates a user without interaction between the browser and user;
- supports arbitrary structure of the login page;
- logs a user out by canceling an ID token.

**Limitations**

- doesn't expect the consent page after the login page.

**Requirements**

- Google Chrome 70 or higher.

## Installing

From sources:

```bash
git clone https://github.com/i-core/tokget
cd tokget
go install ./...
```

From Docker:

```bash
docker pull icoreru/tokget:v1.0.0
```

Download binary:

```bash

curl -Lo /tmp/tokget_linux_amd64.tar.gz 'https://github.com/i-core/tokget/releases/download/v1.0.0/tokget_linux_amd64.tar.gz'
tar -xzf /tmp/tokget_linux_amd64.tar.gz -C /usr/local/bin

# In alpine linux (as it does not come with curl by default)
wget -P /tmp 'https://github.com/i-core/tokget/releases/download/v1.0.0/tokget_linux_amd64.tar.gz'
tar -xzf /tmp/tokget_linux_amd64.tar.gz -C /usr/local/bin
```

## Usage

To authenticate a user `tokget`:
- opens the OpenID Connect Provider's login page in a Google Chrome,
- emulates input user's name and password,
- emulates clicking the login button,
- waits for successful authentication and return an access token and ID token.

Run `tokget -h` to see a list of available commands.

### Login

In terminal:

```bash
tokget login -e https://openid-connect-provider -c client-id -u username --pwd-std
```

**Note** Google Chrome must be in `$PATH`.

Via Docker:


```bash
docker run --name tokget --rm -it icoreru/tokget:v1.0.0 login -e https://openid-connect-provider -c client-id -u username -pwd-stdin
```

**Note** Image `icoreru/tokget` already contains Google Chrome so you don't need to run Google Chrome manually.

To find the username field, password field and login button `tokget` uses CSS selectors.
If the username field, password field and the login button doesn't match to the default CSS selectors
you can override this CSS selectors:

```bash
tokget --remote-chrome http://localhost:9222 login  \
        --username-field "#email"                   \
        --password-field "#pass"                    \
        --submit-button "#submit"                   \
        --error-message "#error"                    \
        -e https://openid-connect-provider          \
        -c client-id                                \
        -u username                                 \
        -p password
```

**Note** `tokget` searches elements on a page using function `document.querySelector()`
so each your CSS selector should match to only one element.

### Logout

In terminal:

```bash
tokget logout -e https://openid-connect-provider -t id_token
```

Via Docker:


```bash
docker run --name tokget --rm -it icoreru/tokget:v1.0.0 logout -e https://openid-connect-provider  -t id_token
```

### Remote Google Chrome

By default `tokget` starts a new Google Chrome process. But you can use an existed Google Chrome process.
This Google Chrome process should be run with enabled debugger, for example:

```bash
chrome --no-sandbox --remote-debugging-address=0.0.0.0 --remote-debugging-port=9222
```

After that you should run `tokget` with option `--remote-chrome`:

```bash
tokget --remote-chrome http://localhost:9222 login -e https://openid-connect-provider -c client-id -u username -p password
```

## Contributing

Thanks for your interest in contributing to this project.
Get started with our [Contributing Guide][contrib].

## License

The code in this project is licensed under [MIT license][license].

[doc-img]: https://godoc.org/github.com/i-core/tokget?status.svg
[doc]: https://godoc.org/github.com/i-core/tokget

[build-img]: https://travis-ci.com/i-core/tokget.svg?branch=master
[build]: https://travis-ci.com/i-core/tokget

[codecov-img]: https://codecov.io/gh/i-core/tokget/branch/master/graph/badge.svg
[codecov]: https://codecov.io/gh/i-core/tokget

[goreport-img]: https://goreportcard.com/badge/github.com/i-core/tokget
[goreport]: https://goreportcard.com/report/github.com/i-core/tokget

[contrib]: https://github.com/i-core/.github/blob/master/CONTRIBUTING.md
[license]: LICENSE

[oidc-spec-core]: https://openid.net/specs/openid-connect-core-1_0.html
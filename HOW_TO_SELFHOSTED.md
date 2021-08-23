# Creating self-hosted runners for Racket CI

Before doing so consider the security issues mentioned by GitHub:
https://docs.github.com/en/actions/hosting-your-own-runners/about-self-hosted-runners#self-hosted-runner-security-with-public-repositories

Assuming a Linux box here with the regular command-line tools installed.
1. Create a racketci user and add it to the docker group
```shell
$ sudo adduser racketci
$ sudo usermod -aG docker racketci
```

2. Download and install github runner under the racketci user
```
$ su - racketci
$ mkdir actions-runner && cd actions-runner
$ curl -O -L https://github.com/actions/runner/releases/download/v2.280.3/actions-runner-linux-x64-2.280.3.tar.gz
$ tar xzf ./actions-runner-linux-x64-2.280.3.tar.gz
```

3. Request a self-hosted runner token from @samth or @mflatt and setup the runner. These tokens expire after a while if you don't use them, so request them _only_ once you're ready to set this up.
```
$ TOKEN=XXXXXX # Replace XXXXXX with the token they send you.
$ ./config.sh --url https://github.com/racket --token $TOKEN
```

The configuration will ask you for a name, I suggest you use a name that includes your github username for easier id like `<username>-racketci-NN`, where `NN` is a number. If you configure two runners then you would have `NN=01` and `NN=02`.
Then as an extra label, enter your username. If all goes ok you should see: `√ Runner successfully added`.

For `work folder` enter: `/home/racketci/_work`.

4. Add the runner to the system start.

```shell
$ sudo su
$ cd /home/racketci/actions-runner
$ ./svc.sh install racketci
```

5. All set - now ensure that the user `racketci` cannot read any file *outside* its home.

> # cmd/retry
>
> `retry` provides functionality to repeat terminal commands.

## Concept

```bash
$ retry -limit=3 -backoff=lin[10ms] -- curl example.com
```

## Documentation

```
usage: retry [-timeout timeout] [strategy flags] -- command

The strategy flags
    --infinite
        Infinite creates a Strategy that will never stop repeating.

    -limit=X
        Limit creates a Strategy that limits the number of attempts that Retry will
        make.

    -delay=Xs
        Delay creates a Strategy that waits the given duration before the first
        attempt is made.

    -wait=Xs,...
        Wait creates a Strategy that waits the given durations for each attempt after
        the first. If the number of attempts is greater than the number of durations
        provided, then the strategy uses the last duration provided.

    -backoff=:algorithm
        Backoff creates a Strategy that waits before each attempt, with a duration as
        defined by the given backoff.Algorithm.

    -tbackoff=":algorithm :transformation"
        BackoffWithJitter creates a Strategy that waits before each attempt, with a
        duration as defined by the given backoff.Algorithm and jitter.Transformation.

:algorithm
    inc{Xs,Ys}
        Incremental creates a Algorithm that increments the initial duration
        by the given increment for each attempt.

    lin{Xs}
        Linear creates a Algorithm that linearly multiplies the factor
        duration by the attempt number for each attempt.

    exp{Xs,Y}
        Exponential creates a Algorithm that multiplies the factor duration by
        an exponentially increasing factor for each attempt, where the factor is
        calculated as the given base raised to the attempt number.

    binexp{Xs}
        BinaryExponential creates a Algorithm that multiplies the factor
        duration by an exponentially increasing factor for each attempt, where the
        factor is calculated as "2" raised to the attempt number (2^attempt).

    fib{Xs}
        Fibonacci creates a Algorithm that multiplies the factor duration by
        an increasing factor for each attempt, where the factor is the Nth number in
        the Fibonacci sequence.

:transformation
    full
        Full creates a Transformation that transforms a duration into a result
        duration in [0, n) randomly, where n is the given duration.

        The given generator is what is used to determine the random transformation.
        If a nil generator is passed, a default one will be provided.

        Inspired by https://www.awsarchitectureblog.com/2015/03/backoff.html

    equal
        Equal creates a Transformation that transforms a duration into a result
        duration in [n/2, n) randomly, where n is the given duration.

        The given generator is what is used to determine the random transformation.
        If a nil generator is passed, a default one will be provided.

        Inspired by https://www.awsarchitectureblog.com/2015/03/backoff.html

    dev{X}
        Deviation creates a Transformation that transforms a duration into a result
        duration that deviates from the input randomly by a given factor.

        The given generator is what is used to determine the random transformation.
        If a nil generator is passed, a default one will be provided.

        Inspired by https://developers.google.com/api-client-library/java/google-http-java-client/backoff

    ndist{X}
        NormalDistribution creates a Transformation that transforms a duration into a
        result duration based on a normal distribution of the input and the given
        standard deviation.

        The given generator is what is used to determine the random transformation.
        If a nil generator is passed, a default one will be provided.
```

### Complex example

```
$ retry -limit=3 -backoff=lin[10ms] -- /bin/sh -c 'echo "trying..."; exit 1'
trying...
[INFO] #2 attempt at 17.636458ms...
trying...
[INFO] #3 attempt at 48.287964ms...
trying...
[ERROR] error occurred: "exit status 1"
$ retry -timeout=500ms --infinite -- /bin/sh -c 'echo "trying..."; exit 1'
trying...
...
trying...
[INFO] #N attempt at 499.691521ms...
[ERROR] error occurred: "context deadline exceeded"
```

## Installation

### Brew

```bash
$ brew install kamilsk/tap/retry
```

### Binary

```bash
$ export SEM_V=3.0.0    # all available versions are on https://github.com/kamilsk/retry/releases
$ export REQ_OS=Linux   # macOS and Windows are also available
$ export REQ_ARCH=64bit # 32bit is also available
$ wget -q -O retry.tar.gz \
      https://github.com/kamilsk/retry/releases/download/${SEM_V}/retry_${SEM_V}_${REQ_OS}-${REQ_ARCH}.tar.gz
$ tar xf retry.tar.gz -C "${GOPATH}"/bin/
$ rm retry.tar.gz
```

### From source code

```bash
$ go get -d github.com/kamilsk/retry
$ cd "${GOPATH}"/src/github.com/kamilsk/retry
$ make cmd-deps-local # or cmd-deps, if you don't have the dep binary but have the docker
$ make cmd-install
```

## Command-line completion

### Useful articles

- [Command-line completion | Docker Documentation](https://docs.docker.com/compose/completion/)

### Bash

coming soon...


### Zsh

coming soon...
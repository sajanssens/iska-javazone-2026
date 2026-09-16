### Three environments, three truths

| Local                    | CI                           | Prod                       |
|--------------------------|------------------------------|----------------------------|
| README, compose, Vagrant | YAML, apt-get, setup actions | Dockerfile, shell commands |

<!-- .element: class="kc-table" -->

Fix one, break another.
<!-- .element: class="fragment" -->

---

### Hello Nix!

- <!-- .element: class="fragment" -->
  Powerful cross-platform package management tool.
- <!-- .element: class="fragment" -->
  Get a consistent environment across
  - <!-- .element: class="fragment" -->
    development (*nix i.e. Linux and Mac)
  - <!-- .element: class="fragment" -->
    CI
  - <!-- .element: class="fragment" -->
    production

---


### One description, every environment

- <!-- .element: class="fragment" -->
  Nix **builds** the app
- <!-- .element: class="fragment" -->
  Docker only **ships** it
- <!-- .element: class="fragment" -->
  Final image `FROM scratch`, no Nix inside

<!-- .slide: class="is-fancy1" -->

---

### Steps

1. <!-- .element: class="fragment" -->
   Write Nix code to describe **how to build and run** your application.
2. <!-- .element: class="fragment" -->
   Use a `Dockerfile` and the official Nix image to **build your application** using Nix.
3. <!-- .element: class="fragment" -->
   Use a multi-stage build `FROM scratch` to copy your built application into the smallest possible image.

---

### Example

<!-- .slide: class="is-fancy1" -->

---

#### The app

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello_world():
    return "<p>Hello, World!</p>"
```

Python

---

#### Write Nix code

- <!-- .element: class="fragment" -->
  Nix Flake
  - <!-- .element: class="fragment" -->
    Describes how to create development environments, build packages, etc.
  - <!-- .element: class="fragment" -->
    A bit like `package.json`.


```
{
  description = "flask-example";

  inputs = {
    ...
  };

  outputs = { self, nixpkgs, flake-utils }:
    ...
        # Development environment
        devShell = mkShell {
          name = "flask-example";
          nativeBuildInputs = [ python3 poetry ];
        };

        # Runtime package
        packages.app = poetry2nix.mkPoetryApplication {
          projectDir = ./.;
        };

        defaultPackage = packages.app;
      }
    );
}
```
<!-- .element: class="fragment" -->

---

### Build the app

Install and use `nix`.

```bash
$ nix build
...
$ result/bin/app
 * Serving Flask app 'src.app'
 * Debug mode: off
 * Running on http://127.0.0.1:5000
Press CTRL+C to quit
```

--- 

### Dockerfile

Simplified...

```dockerfile [1-2|6|8-9|11-15|16-19| ]
# Nix builder
FROM nixos/nix:latest AS builder

(...)

RUN nix (...) build

# Create the nix-store-closure
...

# Final image is based on scratch. We copy a bunch of Nix dependencies
# but they're fully self-contained so we don't need Nix anymore.
FROM scratch

WORKDIR /app

# Copy nix-store-closure and app
COPY --from=builder /tmp/nix-store-closure /nix/store
COPY --from=builder /tmp/build/result /app

CMD ["/app/bin/app"]
```

---

### Trying it...


```bash
$ docker build -t flask-example:dev .
...

$ docker run --rm flask-example:dev
 * Serving Flask app 'src.app'
 * Debug mode: off
 * Running on http://127.0.0.1:5000
Press CTRL+C to quit
```

---

### Downsides
- <!-- .element: class="fragment" -->
  Requires Nix knowledge
- <!-- .element: class="fragment" -->
  Docker image layers are not optimal.
  - <!-- .element: class="fragment" -->
    `RUN nix build` produces a giant layer with all the dependencies in it.
  - <!-- .element: class="fragment" -->
    build-time is really fast
  - <!-- .element: class="fragment" -->
    not optimal for caching layers

---

### Takeaway

> Stop describing your environment three times. <br>
> Use Nix + Docker if Dev/CI/Prod keep drifting.




Notes:

- Nix plus Docker alone is harder than Docker alone
- Only pays off when the same config also drives dev and CI
- Learning curve is real, point at Zero to Nix
- Takeaway: stop describing your environment three times

---

### Like it? See...

![img.png](../../img/06-docker-and-nix/img.png)

<!-- .slide: class="is-fancy1" -->

### Three environments, three truths

| Local                    | CI                           | Prod                       |
|--------------------------|------------------------------|----------------------------|
| README, compose, Vagrant | YAML, apt-get, setup actions | Dockerfile, shell commands |

<!-- .element: class="kc-table" -->

Fix one, break another.
<!-- .element: class="fragment" -->

---

### Hello Nix!

- Powerful cross-platform package management tool. 
- Get a consistent environment across 
  - development (*nix i.e. Linux and Mac)
  - CI
  - production

---


### One description, every environment

- Nix **builds** the app
- Docker only **ships** it
- Final image `FROM scratch`, no Nix inside

<!-- .slide: class="is-fancy1" -->

---

### Steps

1. Write Nix code to describe **how to build and run** your application.
2. Use a `Dockerfile` and the official Nix image to **build your application** using Nix.
3. Use a multi-stage build `FROM scratch` to copy your built application into the smallest possible image. 

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

- Nix Flake
  - Describes how to create development environments, build packages, etc. 
  - A bit like `package.json`.


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

---

### Build the app

Install and use `nix ...`.

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

```dockerfile
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

### Try it!


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
- Requires Nix knowledge
- Docker image layers are not optimal.
  - `RUN nix build` produces a giant layer with all the dependencies in it.
  - build-time is really fast
  - not optimal for caching layers

---

### Worth it?

| Yes, if                        | No, if                       |
|--------------------------------|------------------------------|
| Dev, CI and prod keep drifting | Only ever building one image |

<!-- .element: class="kc-table" -->

Notes:

- Nix plus Docker alone is harder than Docker alone
- Only pays off when the same config also drives dev and CI
- Learning curve is real, point at Zero to Nix
- Takeaway: stop describing your environment three times

---

### Like it? See...

![img.png](../../img/06-docker-and-nix/img.png)

<!-- .slide: class="is-fancy1" -->

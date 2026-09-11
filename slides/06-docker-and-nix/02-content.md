### Three environments, three truths

| Local                  | CI                          | Prod                     |
| ---------------------- | --------------------------- | ------------------------ |
| README, compose, Vagrant | YAML, apt-get, setup actions | Dockerfile, shell commands |

<!-- .element: class="kc-table" -->

Fix one, break another
<!-- .element: class="fragment" -->

Notes:

- Same software, described three times
- Classic: green check on the PR, broken runtime
- Not hard individually, brittle together

---

### One description, every environment

- Nix builds the app
- Docker only ships it
- Final image `FROM scratch`, no Nix inside

<!-- .slide: class="is-fancy1" -->

Notes:

- Big idea: `flake.nix` is the single source of truth for dev, CI and prod
- Dockerfile stays a plain Dockerfile, existing tooling keeps working
- Multi-stage: build with Nix, copy the result out
- Payoff compounds, one config feeds dev and CI too

---

### The source of truth

```nix
outputs = { self, nixpkgs, flake-utils }: ... rec {
  devShell = mkShell {                        # dev + CI
    nativeBuildInputs = [ jdk21 maven ];
  };

  packages.app = maven.buildMavenPackage {    # runtime
    pname = "hello";
    src = ./.;
    mvnHash = "sha256-...";
  };
}
```

<!-- .element: class="kc-smaller" -->

`nix build` → `./result/bin/app`
<!-- .element: class="fragment" -->

Notes:

- Rest of the flake is boilerplate, only these two bits matter
- Nix has first-class knowledge of Maven, Gradle, npm, Go
- `mvnHash` pins the dependency set, same jars everywhere
- result/bin/app depends on nothing from your machine
- No conflict with whatever JDK is on your laptop

---

### The Dockerfile, unchanged forever

```dockerfile
FROM nixos/nix:latest AS builder
COPY . /tmp/build
WORKDIR /tmp/build
RUN nix --extra-experimental-features "nix-command flakes" build
RUN cp -R $(nix-store -qR result/) /tmp/nix-store-closure

FROM scratch
WORKDIR /app
COPY --from=builder /tmp/nix-store-closure /nix/store
COPY --from=builder /tmp/build/result /app
CMD ["/app/bin/app"]
```

<!-- .element: class="kc-smaller" -->

Notes:

- Under 15 lines, never changes again when dependencies change
- `nix-store -qR` gives the closure, smallest set of files needed to run
- Scratch image: no distro, no package manager, tiny attack surface
- Honest downside: one giant layer, poor caching on redeploy

---

### Worth it?

| Yes, if                        | No, if                      |
| ------------------------------ | --------------------------- |
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

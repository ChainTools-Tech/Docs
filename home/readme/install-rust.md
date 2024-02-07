# Install Rust



{% embed url="https://odysee.com/@ChainToolsAcademy:b/RustInstall:e" %}
Video demonstrates steps described below.
{% endembed %}

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

#### Environment

Once Rust is installed environment has to be modified, so binaries can be found.

```bash
source $HOME/.cargo/env
```

Content of $HOME/.cargo/env looks like:

```bash
#!/bin/sh
# rustup shell setup
# affix colons on either side of $PATH to simplify matching
case ":${PATH}:" in
    *:"$HOME/.cargo/bin":*)
        ;;
    *)
        # Prepending path in case a system-installed rustc needs to be overridden
        export PATH="$HOME/.cargo/bin:$PATH"
        ;;
esac
```

### Update Rust

```bash
rustup update nightly && rustup update stable
rustup target add wasm32-unknown-unknown --toolchain nightly
```

### Install wasm-pack

```bash
curl https://rustwasm.github.io/wasm-pack/installer/init.sh -sSf | sh
```

# Docker

[![Build Status](https://travis-ci.org/ghmlee/rust-docker.svg)](https://travis-ci.org/ghmlee/rust-docker)

This is a Docker Remote API binding in Rust. Documentation is available [here](https://ghmlee.github.io/rust-docker/doc/docker).

## Quick start

```
[dependencies]
docker = "0.0.40"
```

```rust
extern crate docker;

use docker::Docker;

fn main() {
    let docker = match Docker::connect("unix:///var/run/docker.sock") {
    	Ok(docker) => docker,
        Err(e) => { panic!("{}", e); }
    };
}
```

## Debug
* Rust (>= v1.1.0)
* Docker (>= v1.5.0)

## Examples

### Containers

```rust
extern crate docker;

use docker::Docker;

fn main() {
    let docker = match Docker::connect("unix:///var/run/docker.sock") {
    	Ok(docker) => docker,
        Err(e) => { panic!("{}", e); }
    };

    let containers = match docker.get_containers(false) {
        Ok(containers) => containers,
        Err(e) => { panic!("{}", e); }
    };
}
```

### Stats

```rust
extern crate docker;

use docker::Docker;

fn main() {
    let docker = match Docker::connect("unix:///var/run/docker.sock") {
    	Ok(docker) => docker,
        Err(e) => { panic!("{}", e); }
    };

    let containers = match docker.get_containers(false) {
        Ok(containers) => containers,
        Err(e) => { panic!("{}", e); }
    };

    let stats = match docker.get_stats(&containers[0]) {
        Ok(stats) => stats,
        Err(e) => { panic!("{}", e); }
    };
}
```

### Images

```rust
extern crate docker;

use docker::Docker;

fn main() {
    let docker = match Docker::connect("unix:///var/run/docker.sock") {
    	Ok(docker) => docker,
        Err(e) => { panic!("{}", e); }
    };

    let images = match docker.get_images(false) {
        Ok(images) => images,
        Err(e) => { panic!({}, e); }
    };
}

```

### Info

```rust
extern crate docker;

use docker::Docker;

fn main() {
    let docker = match Docker::connect("unix:///var/run/docker.sock") {
    	Ok(docker) => docker,
        Err(e) => { panic!("{}", e); }
    };

    let info = match docker.get_system_info() {
        Ok(info) => info,
        Err(e) => { panic!("{}", e); }
    };
}
```

### Processes

```rust
extern crate docker;

use docker::Docker;

fn main() {
    let docker = match Docker::connect("unix:///var/run/docker.sock") {
    	Ok(docker) => docker,
        Err(e) => { panic!("{}", e); }
    };

    let containers = match docker.get_containers(false) {
        Ok(containers) => containers,
        Err(e) => { panic!("{}", e); }
    };

    let processes = match docker.get_processes(&containers[0]) {
        Ok(processes) => processes,
        Err(e) => { panic!("{}", e); }
    };
}
```

### Filesystem changes

```rust
extern crate docker;

use docker::Docker;

fn main() {
    let docker = match Docker::connect("unix:///var/run/docker.sock") {
    	Ok(docker) => docker,
        Err(e) => { panic!("{}", e); }
    };

    let containers = match docker.get_containers(false) {
        Ok(containers) => containers,
        Err(e) => { panic!("{}", e); }
    };

    let changes = match docker.get_filesystem_changes(&containers[0]) {
        Ok(changes) => changes,
        Err(e) => { panic!("{}", e); }
    };
}
```

### Export a container

```rust
extern crate docker;

use docker::Docker;

fn main() {
    let docker = match Docker::connect("unix:///var/run/docker.sock") {
    	Ok(docker) => docker,
        Err(e) => { panic!("{}", e); }
    };

    let containers = match docker.get_containers(false) {
        Ok(containers) => containers,
        Err(e) => { panic!("{}", e); }
    };

    let bytes = match docker.export_container(&containers[0]) {
        Ok(bytes) => bytes,
        Err(e) => { panic!("{}", e); }
    };
}
```

### Create an image

```rust
extern crate docker;

use docker::Docker;

fn main() {
    let docker = match Docker::connect("unix:///var/run/docker.sock") {
    	Ok(docker) => docker,
        Err(e) => { panic!("{}", e); }
    };

    let image = "debian".to_string();
    let tag = "latest".to_string();
    
    let statuses = match docker.create_image(image, tag) {
        Ok(statuses) => statuses,
        Err(e) => { panic!("{}", e); }
    };
    
    match statuses.last() {
        Some(last) => {
            println!("{}", last.clone().status.unwrap());
        }
        None => { println!("none"); }
    }
}
```

### Ping the docker server

```rust
extern crate docker;

use docker::Docker;

fn main() {
    let docker = match Docker::connect("unix:///var/run/docker.sock") {
    	Ok(docker) => docker,
        Err(e) => { panic!("{}", e); }
    };
    
    let ping = match docker.ping() {
        Ok(ping) => ping,
        Err(e) => { panic!("{}", e); }
    };
}
```

### Show the docker version information

```rust
extern crate docker;

use docker::Docker;

fn main() {
    let docker = match Docker::connect("unix:///var/run/docker.sock") {
    	Ok(docker) => docker,
        Err(e) => { panic!("{}", e); }
    };
    
    let version = match docker.get_version() {
        Ok(version) => version,
        Err(e) => {panic!("{}",e)}
    };
}
```

## Docker Toolbox

By default, `Docker Toolbox` runs `docker` with TLS enabled. It auto-generates certificates. The `docker-machine` will copy them to `~/.docker/machine/certs` on the host machine once the VM has started.

### Example

```rust
extern crate docker;

use docker::Docker;
use std::path::Path;

fn main() {
    let key = Path::new("/Users/<username>/.docker/machine/certs/key.pem");
    let cert = Path::new("/Users/<username>/.docker/machine/certs/cert.pem");
    let ca = Path::new("/Users/<username>/.docker/machine/certs/ca.pem");

    let mut docker = match Docker::connect("tcp://192.168.99.100:2376") {
    	Ok(docker) => docker,
        Err(e) => { panic!("{}", e); }
    };
    docker.set_tls(&key, &cert, &ca).unwrap();
}
```

## Boot2Docker

By default, `boot2docker` runs `docker` with TLS enabled. It auto-generates certificates and stores them in `/home/docker/.docker` inside the VM. The `boot2docker` up command will copy them to `~/.boot2docker/certs` on the host machine once the VM has started, and output the correct values for the `DOCKER_CERT_PATH` and `DOCKER_TLS_VERIFY` environment variables.

### Example

```rust
extern crate docker;

use docker::Docker;
use std::path::Path;

fn main() {
    let key = Path::new("/Users/<username>/.boot2docker/certs/boot2docker-vm/key.pem");
    let cert = Path::new("/Users/<username>/.boot2docker/certs/boot2docker-vm/cert.pem");
    let ca = Path::new("/Users/<username>/.boot2docker/certs/boot2docker-vm/ca.pem");

    let mut docker = match Docker::connect("tcp://192.168.59.103:2376") {
    	Ok(docker) => docker,
        Err(e) => { panic!("{}", e); }
    };
    docker.set_tls(&key, &cert, &ca).unwrap();
}
```

## Contributing

1. Fork it
2. Create your a new remote upstream repository (`git remote add upstream git@github.com:ghmlee/rust-docker.git`)
3. Commit your changes (`git commit -m 'Add some feature'`)
4. Push to the branch (`git push origin your-branch`)
5. Create new Pull Request
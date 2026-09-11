# Helm's deep

The objective is to standardize Kubernetes deployment templates into parameterizable formats and deliver a CLI tool capable of assembling diverse deployment units.

This project provides the `hammerhand` CLI tool, which you can symlink into a directory on your $PATH.


## Usage example (prod functions)

Run on vanilla k8s cluster.

```bash
export K8S_CLUSTER=production
export INGRESS_ENTRYPOINT=websecure

# deploy HTTP only ingress (work in progress on a production ready solution. Now it is manually configured now on production server)
hammerhand u ingress/traefik ingress_http

export NAMESPACE=ipfs
export PRE_PIN=true

# deploy IPFS core service (for details consult: `hammerhand u service/ipfs` unit)
hammerhand u service/ipfs deploy_service

# deploy javaexperience.eu pages

## deploy git.javaexperience.eu 
INGRESS_NAME=git-jvx INGRESS_HOST=git.javaexperience.eu IPFS_CID=QmUt4yYjUg9QMzjQyVN9CsDEbWbiPYjNLrtKw9LW5zKT5n IPFS_CID_PIN=true hammerhand u service/ipfs serve_page

## deploy maven.javaexperience.eu 
INGRESS_NAME=mvn-jvx INGRESS_HOST=maven.javaexperience.eu IPFS_CID=QmQX53dGEP2UwYg3bo5Ksd4eenghkHQGyWV7NwkRSfRyFa IPFS_CID_PIN=true hammerhand u service/ipfs serve_page

# deploy redirect on main domain
NAME=jvx javaexperience.eu REDIRECT_TARGET=https://maven.javaexperience.eu hammerhand u tools/redirect deploy
```

## Usage example (dev functions)

Run on kind k8s cluster, DNS `*.k8s.static.dev.intra.net` is set to kind container IP.

```bash

export DOMAIN_ROOT=k8s.static.dev.intra.net

# deploys plain HTTP ingress as daemonset.
hammerhand u ingress/traefik ingress_http
# deploys a debug container with debug services
hammerhand u dev/debug deploy_devops

# deploys an image registry, you can push and pull images to the node and expose on host's tcp/5000 port
hammerhand u service/registry deploy_registry
TYPE=TCP PORT=5000 SERVICE=registry-service SERVICE_PORT=5000 BIND_ADDRESS=127.0.0.1 hammerhand u ingress/serviceport ingress_port

# ipfs: install IPFS as core service
hammerhand u service/ipfs deploy_service

# deploy javaexperience maven repo (sserve static files)
INGRESS_NAME=maven INGRESS_HOST=maven.${DOMAIN_ROOT} IPFS_CID=bafybeibamkzqsjwbvkj5igauuvliffqebdgn2ydiyqt3idpd3vh7hd55nu hammerhand u service/ipfs serve_page

# Serve szabilinux.hu website
INGRESS_NAME=szabilinux INGRESS_HOST=szabilinux.${DOMAIN_ROOT} IPFS_CID=bafybeig2i4gtt6azojl32noco3ov6e6o7qaxasu2q32qlzssvcgdw3kdbi hammerhand u service/ipfs serve_page

# Open up IPFS endpoint, you can directly access http://ipfs.${DOMAIN_ROOT}/ipfs/$CID
INGRESS_NAME=ipfs-direct-gateway INGRESS_HOST=ipfs.${DOMAIN_ROOT} SERVICE=ipfs-service PORT=8080 hammerhand u ingress/traefik ing_srv_host

# deploys a supabase stack and set up ingress
hammerhand u apps/supabase deploy
INGRESS_NAME=supabase-studio INGRESS_HOST=supabase-studio.${DOMAIN_ROOT} SERVICE=supabase-demo-supabase-studio PORT=3000 hammerhand u ingress/traefik ing_srv_host
INGRESS_NAME=supabase-studio-api INGRESS_HOST=supabase-api.${DOMAIN_ROOT} SERVICE=supabase-demo-supabase-kong PORT=8000 hammerhand u ingress/traefik ing_srv_host

# deploys a testapp
HOST=test.${DOMAIN_ROOT} hd u apps/testwebapp deploy

# deploys static file hosting
HOST=files.${DOMAIN_ROOT} NAME=files DIR_LISTING=true SERVE_PATH=/static_files hammerhand u tools/servedir deploy
```
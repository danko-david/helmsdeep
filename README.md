# Helm's deep

The objective is to standardize Kubernetes deployment templates into parameterizable formats and deliver a CLI tool capable of assembling diverse deployment units.

This project provides the `hammerhand` CLI tool, which you can symlink into a directory on your $PATH.


## Usage example
```
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
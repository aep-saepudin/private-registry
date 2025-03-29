# Docker Registry Setup Guide

## Authentication Setup

1. Create authentication credentials for the registry:
```bash
# Create htpasswd file with username 'aepfeha' (you can change the username and password)
docker run --rm --entrypoint htpasswd httpd:2 -Bbn aep Xmantap123! > auth/htpasswd
```

## SSL Certificate Setup

1. Generate self-signed certificates:
```bash
# Create directory for certificates if it doesn't exist
mkdir -p certs

# Generate private key
openssl genpkey -algorithm RSA -out certs/privkey.pem

# Create certificate signing request (fill in the prompts with your registry domain information)
openssl req -new -key certs/privkey.pem -out certs/server.csr

# Generate self-signed certificate (valid for 365 days)
openssl x509 -req -days 365 -in certs/server.csr -signkey certs/privkey.pem -out certs/cert.pem

# Create fullchain.pem by copying the certificate (since it's self-signed, cert.pem is the full chain)
cp certs/cert.pem certs/fullchain.pem
```

Note: For production use, it's recommended to use a proper Certificate Authority (CA) instead of self-signed certificates. The certificates should be placed in the `certs` directory as `fullchain.pem` and `privkey.pem` as specified in the docker-compose configuration.

## Registry Information

Registry URL: registry.fehagrc.com:4443

## Usage

1. After setting up authentication and certificates, start the registry using docker-compose:
```bash
docker-compose up -d
```

2. Login to the registry:
```bash
docker login registry.fehagrc.com:4443
```

3. Push images using:
```bash
docker push registry.fehagrc.com:4443/your-image:tag
```
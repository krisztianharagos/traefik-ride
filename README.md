# traefik-ride

## certificate generation

https://smallstep.com/docs/step-cli/installation/
https://smallstep.com/docs/step-cli/basic-crypto-operations/#create-and-work-with-x509-certificates

```bash
brew install step

step certificate create "My Root CA" root-ca.crt root-ca.key \
    --profile root-ca --no-password --insecure
step certificate create "My Intermediate CA" intermediate-ca.crt intermediate-ca.key \
    --profile intermediate-ca --ca ./root-ca.crt --ca-key ./root-ca.key \
    --no-password --insecure
step certificate create example.com server.crt server.key \
    --profile leaf --ca ./intermediate-ca.crt --ca-key ./intermediate-ca.key \
    --no-password --insecure

cat server.crt intermediate-ca.crt root-ca.crt > fullchain.crt


step certificate verify fullchain.crt --roots root-ca.crt
# no output is the successful verification    
```

## docker-compose

```bash
sudo vi /etc/hosts
    127.0.0.1 example.com # add

docker-compose up
```

## check

```bash
#step certificate inspect https://example.com --roots "./root-ca.crt" --format pem --bundle
#step certificate inspect https://example.com --roots "./root-ca.crt" --format json --bundle

step certificate inspect https://example.com --roots "./root-ca.crt" --format json --bundle | grep subject_dn # best for shallow verfication

curl -ks https://example.com
```
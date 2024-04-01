# TLS_keys
# Create CA cert and key
# issuer/Subjet "CN=My custom CA, DC=localhost"
openssl req \
    -x509 \
    -new \
    -nodes \
    -keyout my-custom-ca.key \
    -sha256 \
    -days 3650 \
    -out my-custom-ca.crt \
    -subj '/CN=My custom CA/DC=localhost'
# Add CA to trust anchor
sudo trust anchor --store my-custom-ca.crt
sudo update-ca-trust
# Create a CSR and key
openssl req \
    -new \
    -newkey rsa:2048 \
    -nodes \
    -keyout localhost.key \
    -sha256 \
    -out localhost.csr \
    -subj '/CN=My custom certificate/DC=localhost' \
    -addext 'subjectAltName=DNS:localhost' \
    -addext 'basicConstraints=critical,CA:FALSE' \
    -addext 'keyUsage=digitalSignature' \
    -addext 'extendedKeyUsage=serverAuth'
# sign CSR with CA
openssl x509 \
    -req \
    -days 365 \
    -in localhost.csr \
    -CA my-custom-ca.crt \
    -CAkey my-custom-ca.key \
    -out localhost.crt \
    -set_serial 01 \
    -sha256 \
    -copy_extensions copy
    

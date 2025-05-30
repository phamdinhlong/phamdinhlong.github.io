# Nginx: Local SSL & HTTPS

Tags: Webserver, debian, nginx

### Generating the Private Key and Root Certificate

```bash
mkdir ~/certs
cd ~/certs

# Generate rootCA's private key and self-signed certificate (10yrs)
openssl req -x509 -newkey rsa:2048 -days 3650 -noenc -sha256 -keyout root-ca.key -out root-ca.pem -subj "/C=VN/CN=Local Root CA"

## Adding the root certificate to MacOS
sudo security add-trusted-cert -d -r trustRoot -k "/Library/Keychains/System.keychain" root-ca.pem

## Adding the root certificate to Linux (Debian/Ubuntu)
sudo cp root-ca.pem /usr/local/share/ca-certificates/root-ca.crt
sudo update-ca-certificates

## Adding the root certificate to Windows (powershell as admin)
certutil –addstore -ent "Root" <path-of-certificate-file>
### or delete
certutil -delstore -ent "Root" "Old-Certificate-CommonName"

**## iOS** 
There are two common ways to install a CA’s root certificate on an iOS device for testing purposes:
1. Put the .cer file on a web server and navigate to it in Safari. iOS will then ask you whether you want to install and trust that root certificate.
2. Attach the root certificate to an email and then email it to yourself; when you open the attachment, iOS will ask you whether you want to install and trust that root certificate.
**Important: Regardless of how you install the root certificate you must specifically enable it in Settings > General > About > Certificate Trust Settings.**
```

### Creating CA-Signed Certificates for Your Dev Sites

```bash
# Generate $domain's private key and certificate signing request (CSR)
openssl req -newkey rsa:2048 -noenc -keyout tipitaka.vm.key -out tipitaka.vm.csr -subj "/C=VN/CN=*.tipitaka.vm"

# Use rootCA's private key to sign $domain's CSR and get back the signed certificate
openssl x509 -req -in tipitaka.vm.csr -CA root-ca.pem -CAkey root-ca.key -CAcreateserial -out tipitaka.vm.crt -days 3650 -sha256 -extfile <(echo "subjectAltName=DNS.1:tipitaka.vm,DNS.2:www.tipitaka.vm")

# Copying $domain.crt & $domain.key to /etc/nginx/ssl (creating it if not exist)
sudo cp tipitaka.vm.crt tipitaka.vm.key /etc/nginx/ssl/

```

**3. Thêm 2 dòng sau vào tập tin cấu hình của môi tên miền, nhớ là phải nằm trong server block và listen 443 ssl http2**

```bash
ssl_certificate         /etc/nginx/ssl/domain.crt;
ssl_certificate_key     /etc/nginx/ssl/domain.key;
```

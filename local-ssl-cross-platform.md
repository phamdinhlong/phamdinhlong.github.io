# Local SSL Cross Platform

Tags: virtualbox

```bash
mkdir ~/certs
cd ~/certs
openssl genrsa -des3 -out devCA.key 2048
openssl req -x509 -new -nodes -key devCA.key -sha256 -days 3650 -out devCA.pem

# Adding the Root Certificate to MacOS
sudo security add-trusted-cert -d -r trustRoot -k "/Library/Keychains/System.keychain" devCA.pem

# Adding the Root Certificate to Linux
sudo cp devCA.pem /usr/local/share/ca-certificates/devCA.crt
sudo update-ca-certificates

# Adding the Root Certificate to Windows 10
1. Open the MMC by [press Windows + R, typing "mmc" Open
2. Go to **File** > **Add/Remove Snap-in**
3. Click **Certificates** and Add
4. Select **Computer Account** and click **Next**
5. Select **Local Computer** then click **Finish**
6. Click **OK** to go back to the MMC window
7. Double-click **Certificates (local computer)** to expand the view
8. Select **Trusted Root Certification Authorities**, right-click on **Certificates,** select **All Tasks** then **Import**
9. Click **Next** then **Browse**. Change the certificate extension dropdown next to the filename field to **All Files** (*.*) and locate the *myCA.pem* file, click **Open**, then **Next**
10. Select **Place all certificates in the following store**. “Trusted Root Certification Authorities store” is the default. Click **Next** and **Finish

# Adding the Root Certificate to iOS 14**
If you use something like ngrok to browse to your local development sites on mobile devices, you might need to add the root certificate to these devices. On iOS devices you can do so fairly easily by following these steps:

****1. Email the root certificate to yourself, so you can access it on your iOS device. Make sure to use the default Mail app to access the email.
2. Tap on the attachment in the email on your iOS device. It will prompt you to review the profile in the Settings app.
3. Open the Settings app and click **Profile Downloaded** near the top.
4. Click **Install** in the top right, and then **Install** again on the Warning screen.
5. Once installed, hit **Close** and go back to the main Settings page.
6. Go to **General** > **About**.
7. Scroll to the bottom and click on **Certificate Trust Settings**.
8. Enable your root certificate under “ENABLE FULL TRUST FOR ROOT CERTIFICATES”.
```

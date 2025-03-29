# NSA-VDI-Linux
THis is currently a very rough draft for getting the NSA VDI working on a linux workstation.(beta 0.2 03282028)


-You should be able to find all of these with you PKG Manager. You may need to google around if the package is named differently on you manager.

		pcsc-tools
		libccid
		libpcsc-perl
		libpcsclite1
		pcscd
		vsmartcard-vpcd
		libnss3-tools
		opensc
		opensc-pkcs11

Step 2: Certificates
 
	-Download PKE/PKI certificates. You’re looking for “ PKI CA Certificate Bundles: PKCS#7 for DoD PKI Only - Version 5.13” or high

		https://public.cyber.mil/pki-pke/pkipke-document-library/ 

Using

STEP 3: Citrix Workstation app download

	-Download your version of the Citrix workstation app based on your distros Packet Managers file format. .deb (Debian and Debian based distros)
	-For Arch based systems you want to use the Tarball package. If needed us google to unzip and install. (version 2 of this document will include instructions)

https://www.citrix.com/downloads/workspace-app/linux/workspace-app-for-linux-latest.html 

	-Not the location of your download, most likely in your Downloads directory
	cd into the directory of the file using cd /home/<uname><location of the file>

STEP 4: Install Citrix Workstation

	-Run the following command to install Citrix Workstation: starting with Version 2101, the following interactive prompt appears asking you to install App Protection: Select Yes to continue with the installation with the App Protection component.

	.deb
		sudo apt install -f ./icaclient_<version>._amd64.deb
	.rpm
		yum localinstall ICAClient-rhel-<version>.x86_64.rpm
	Others
		instructions will be included in version 2
		
STEP 5: Create Soft Links

	-This step may or may not be necessary. Remove cacerts and link to /etc/ssl/certs by the following commands:
		THIS STEP IS NOT OPTIONAL! As of now I do not know why you have to do this, but I was unable to get it to work without this step.

		cd /opt/Citrix/ICAClient/keystore/
		sudo rm -rf cacerts
		sudo ln -s /etc/ssl/certs cacerts

STEP 6: Additional Certs

	-The certificate you download will be based on the error you get when trying to log into the desktop environment after you’ve entered your pin on the main vdi.nsa.gov page. 
	-My error was “ You have chosen not to trust DOD SW CA-66, the issuer of the server’s security certificate (SSL error 61))”

https://crl.gds.disa.mil/ 

	-Select the appropriate certificate from the left hand panel and click “SUBMIT SELECTION”
	-Click “download” from the “Certificate Authority Certificate.
		-This will download a .cer file

Step 7: .cer to .pem conversion

	-From the terminal navigate to the directory where the file was downloaded and run the following command. This converts the .cer file to a .pem file.:
		openssl x509 -in <DODSWCA_66.cer> -outform pem -out <DODSWCA_66.pem>

Step 8: Moving the newly created .pem file

	-There are two possible ways this can go depending on if you did Step 5.
	-If you did Step 5, use the following command to copy you .pem file to the correct dir:
		sudo cp /<PATH>/DODSWCA_66.pem /etc/ssl/certs
	-If you did not do Step 5: 
		Sudo cp /<PATH>/DODSWCA_66.pem /opt/Citrix/ICAClient/keystore/cacerts	

	-At this point you should be good to go. However, I did a few more things prior to this that might make a difference.

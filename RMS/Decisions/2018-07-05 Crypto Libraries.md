# Crypto Library for Credential Encryption

Decision date: 2018-07-05  
Approval: Approved  
Approver: Keng   

**This point was discussed on the bi-weekly calls on 2018-07-10 and approved.   **  

The team plans to use the "crypt.jar" library provided by Alan Morgan to perform encryption of IDP and other credentials to be stored in properties files. This is related to story [SC-2159](https://nextlabs.atlassian.net/browse/SC-2159)  

This will provide the same level of protection as the CC does for its credentials.   

Alan Morgan was requested to provide an obfuscated version of the library for commercial shipping since the version in use today is not protected. Alan agreed to ship us a new version protected with Allatori in a few days (from 2018-07-05).  

The crypto library itself is rather easy to use. The `ReversibleEncryptor()` class provides encrypt and decrypt functions. Sample code is included below.   

```
package com.nextlabs.com;

import com.bluejungle.framework.crypt.IDecryptor;
import com.bluejungle.framework.crypt.IEncryptor;
import com.bluejungle.framework.crypt.ReversibleEncryptor;


public class NxlCrypto {
	public static final IEncryptor encryptor = new ReversibleEncryptor();
	public static final IDecryptor decryptor = new ReversibleEncryptor();
	
	public static void main(String[] args) {
		try {
			String rawValue = "123Next!";
			String encryptedValue = NxlCrypto.encryptor.encrypt(rawValue);
			
			System.out.println("Encrypted: [" + encryptedValue + "]");
			System.out.println("Decrypted: [" + decryptor.decrypt(encryptedValue) + "]");
			
		} catch(Exception err) {
			err.printStackTrace();
		}
	}
}
```
This code produces the following output.
```
Encrypted: [... encrypted content deleted ...]
Decrypted: [123Next!]
```
The `crypt.jar` library is planned to be used for:

- The setup phase where the administrator installs the SkyDRM server for the first time and configures the credentials. 
- To update credentials.
- In the SkyDRM code, to read credentials from property files.
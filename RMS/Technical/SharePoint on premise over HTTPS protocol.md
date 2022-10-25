###SharePoint on premise over HTTPS protocol

####Issue

To set up a SharePoint on premise (SPOP) repository, the user should give the site URL as the input information. Currently, our implementation only supports HTTP site URL, which makes functionalities (searching, listing, etc) not secure. We need a solution to support the HTTP protocol.

####Solution

[Commons Virtual File System (VFS)](https://commons.apache.org/proper/commons-vfs/) provides a single API for accessing various different file systems. It presents a uniform view of the files from various different sources, such as the files on local disk, on an HTTP server, or inside a Zip archive. The original VFS library does not support SPOP implementation. We have a NextLabs version of VFS from PS team which supports SPOP. The details are in this [blog](https://md.sphqxe.com/s/B1_G3PRoN). We can use VFS to replace our current logic for supporting a secure connection.

####Notes

+ Currently, SPOP and SharePoint online (SPOL) are sharing some codes. Therefore, we have two options.
    
    - Separate the logic for these two;
    - Or use VFS to implement both.

+ There are also some libraries extending VFS to support Google Drive, Dropbox, and other 3rd party repositories.

####Related Java classes in our codebase

+ `SharePointClient.java`
+ `SharePointOnPremiseRepository.java`





######*Created on 09 May 2019, updated on 09 May 2019*
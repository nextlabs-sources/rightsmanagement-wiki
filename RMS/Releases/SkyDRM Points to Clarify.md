## Older list of Questions

**Tarun**  

1. Installation of SkyDRM RMS requires Onedrive or S3. This is a problem if customer is not using either. If a customer only wants to use dropbox why does he need S3/Onedrive?

2. How to protect a file on local machine? Is this a supported use case with new SkyDRM Desktop or not? Note: usecase is to protect locally and not to upload to SkyDRM

3. Local files continue to be unprotected with SkyDRM. What is the justification for that?

4. Files downloaded from myvault are .nxl

    - How to open these files locally? 
    - How to open these files locally when offline?
    - How to control access to these files using policies?

5. Does each RM plugin require a PDP of its own? If this is the case which component would handle keeping policies current etc.?

6. What all components need to be installed on a client machine for simple EDRM use case i.e. Protect and consume (read/write) Office and PDF files?

7. What all components need to be installed on a client machine for TC EDRM use case 

8. If something doesn't work as expected in 6) or 7) where do we start troubleshooting?

**Tapas**  

9. What APIs does an application plugin (e.g. Office plugin, NX plugin) can call to check whether the user is allowed to Edit/Save/Print/etc the file?

**Sadesh**  

1.	How can user classify a document using the client?

2.	If user classifies a document as Sensitive and a single central policy can support across the projects?

3.	Does the SKYDRM desktop client supports silent installation?

4.	Currently I need to configure a router url when I launch the skydrm desktop client first time. I don’t think an end user will know those IT specific information. Is it possible to bundle he configuration as a part of installation package like the old RMC 8.4.1?

5.	Can user check what rights does he has on the file?

6.	Does skydrm desktop uses the RPM which is needed for opening files in native application without hindrance?

7.	If user logins into SKYDRM and goes to myvault and click the file he get share option, expected was to view the file onclick as we see in my files option.

8.	Since viewer runs in port 8445 and if it has self- signed certificate, user cant view the file and he gets no notifications. In this particular scenario user needs to add an exception to viewer manually by opening the viewer url in the browser. Than user can see the file in viewer. User need to know why he can’t view the file and what he needs to do.

9.	If the hostnames are not DNS resolved, in chrome we can’t login to skydrm. Need clear documentation on what user needs to do.

**Paritosh**   

1.	If the server hardware has a GPU, will docker use it – A question in regard to performance.

2.	How will the solution work when used along with other endpoint products like WDE or OE? Do we still need multiple PDP’s installed on the client?

3.	How do we back up the encryption keys? Steps to recover the documents if the encryption keys are lost or need to be restored from the backup?

        * NXL file reside on file system server, Enterprise IT will take care.
        * Keys are stored in Database (Oracle or MSSql) servers, DBA needs to take care of backup and availability of the database.
        * Need to develop Guidelines for recovery and backup.

4.	Do we support customized overlay on the clients based on policy?

# Product / Feature Implications

1. The viewer container needs a GPU, it needs an [nVidia GPU optimised docker image](https://github.com/NVIDIA/nvidia-docker)

2. Master tenant central policy support must be implemented to support a single central policy across projects. 

3. SkyDRM must support a local / NFS file system to remove the dependency on S3 or OneDrive.

4. How does the admin recover after a DB crash? This needs to be documented.  
Can we recover tokens? How?  
Is there a simple process to perform this recovery? How will it work?
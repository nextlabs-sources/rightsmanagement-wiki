# SkyDRM Discussion Points

## Questions on 2018-09-19
1.	At the moment, we do not have clarity on the interaction between the RPM, SDK, Application plugins, RMD, PDP on a client machine. How do these components work together in the context of central policies, ad-hoc policies, my vault, projects, and local files stores? A high-level architecture diagram will be very helpful. 
2.	How will a client integrate with Policy controller at client side? Does each RM plugin require a PDP of its own? How would we keep policies up-to-date across all the plugins?
3.	Which are the components a user needs to install on her system for a simple EDRM use case, to protect and consume (read/write) office and PDF files? What additional components she need to install if upgrading to Teamcenter EDRM?
4.	How does the central policy come in to play across SkyDRM projects and vault?
5.	What is the user workflow for protection, share, and edit of a local file in an offline or online usage scenario?  

    a.	What is the strategy for protection at rest? How can a user keep her local files protected and avoid any misuse?  

    b.	What is the user workflow when a protected file is downloaded from Teamcenter or SkyDRM my vault for editing?  

    c.	How to open the local NXL files for editing in native apps? Does SkyDRM desktop application use the RPM component for opening files transparently in the native applications?  

    d.	Do we support offline editing protected files?  

    e.	How do we provide access control using policies?  

6.	How will a user classify a document using the desktop client? We couldn’t figure out a way to reclassify a file or view the classifications. (Are we missing something or it’s still in development?)
  
There are some other questions related to diagnostics, troubleshooting, and security.  
  
1.	Have we considered troubleshooting as a part of system architecture? There are so many different components involved, how do we start troubleshooting when something goes wrong at the client site?

2.	What are the steps to recover the encrypted documents when encryption keys are lost? How to back up the encryption keys?

3.	SkyDRM RMS installation requires S3 or OneDrive account information, what if a user doesn't use any of these? Can she use a local file system or another storage service such as Dropbox?
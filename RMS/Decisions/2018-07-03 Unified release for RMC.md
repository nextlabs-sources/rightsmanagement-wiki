# Branding and release: positioning

Date: 2018-07-03  
Approval: Done  
Workflow: Approved by Keng     

~~~
Here is the latest list of external brand name and product names for the upcoming SkyDRM release.  It was confirmed by Keng today.

•	Overall brand name
       o	SkyDRM
•	Server product name
       o	SkyDRM RMS
•	Client product names
       o	SkyDRM Web App
       o	SkyDRM App for Android
       o	SkyDRM App for iOS
       o	SkyDRM Desktop for Windows
       o	SkyDRM Desktop for macOS
•	SDK product names
       o	SkyDRM Client SDK
       o	SkyDRM Server SDK

Please make sure that the correct names appear in all the app UI, installers, Google Play / App Store entries, help files, release note, etc.

Thank you.

--- ayuen

~~~

# Product Offers

The offer will consist of two products:  
- SkyDRM SaaS edition hosted by us and available to the public, and   
- the SkyDRM private cloud edition which is identical to the SaaS edition but hosted and managed by our customer(s)  

The SkyDRM RMS server component will not be sold separately from the SkyDRM web app.

# Points to confirm

In the past we sold the viewers for Office/PDF documents and the viewer for CAD files separately. Do we plan to follow this approach, or sell one single viewer package with all our viewers bundled?   
**Keng: Confirmed on 2018-07-07 that we will not provide one single viewer bundle to all our customers. Email for reference below.  **  


  
> From: Keng Lim   
> Sent: Saturday, 7 July 2018 10:51 AM  
> To: Sriram Ramachandran <Sriram.Ramachandran@nextlabs.com>  
> Subject: RE: [SkyDRM] Portal to note down technical points  
>  
> Due to OEM cost – for example we license CAD viewer from TS3D and Office/PDF/other file types viewing from  Perceptive (which is now Hyland), that is why we are NOT offering one viewer bundle to all customer.  
>   
> We can do that if we believe that is the right thing to do. Alternatively, if Siemens PLM Vis SDK can provide the capability offered by TS3D (HOOPS SDK), we can explore using the PLM Vis SDK instead for the EDRM for Teamcenter product. See a description of the Siemens PLM Vis SDK below.  
>   
> Comprehensive Support for 2D/3D Visualization Formats   
> PLM Vis supports visualization of over 40 popular 2D and 3D formats including NX™, Solid Edge®, Parasolid® XT, VRML, STL and JT – which makes applications powered by PLM Vis extremely versatile.  
>  
> SAP also has its only 3D Viewer and SDK for SAP’s VE format which we have access to, and we believe that most SAP customer who uses SAP EDRM will be mainly interested in the SAP formats, Office, and PDF.  
>  
> Thus we need to design the Viewer to be a pluggable module and we need to be able to plug in various different viewer such as PLM Vis, SAP VE, etc.  
>     

What are the delivery artifacts? Is the following valid?   

    - SkyDRM RMS  
    - SkyDRM Web App  
    - SkyDRM Server SDK  
    - Office / PDF viewer   
    - CAD viewer  
  
** Confirmation from Keng on 2018-07-17:  **  
SkyDRM will have five components:   
 
1. SkyDRM Server (RMS)
2. Clients for 5 platforms (Android, iOS, web, Windows, MacOS)  
3. Application rights manager, for example:  
        * DCE for Teamcenter will become "rights manager for Teamcenter"  
        * SAP EDRM will become "rights manager for SAP"  
4. SDK  
        * Server SDK: This is the RMJavaSDK, mostly for internal use  
        * Client SDK: emphasis on CAD tools, mostly for external use, e.g., with a "rights manager for AutoCAD", etc.   
5. Tools, e.g, smart classifier, which is a standalone tool today but will become part of the SkyDRM family to be used by our enterprise customers to classify/manage their documents
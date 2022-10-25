# Extract Content  
  
This feature in SkyDRM allows the user to extract the content of a protected file and store this content as a native file, e.g., TXT, PDF, and so on.  
  
## Requirements  
  
* Project / Company policy files:
    + Extract content -> can be deployed as a policy
    + On the UX (file listing, viewer), the extract icon should be visible to the user if it is allowed by the policy. Users without extract rights should not see the icon.
* Ad-hoc protected files:
    + For ad-hoc files, users can switch on the extract right. 
    + The switch to turn on or off extract rights should not appear by default.
    + The user must be presented with a "more options" choice. The user clicks on "more options". The switch to toggle extract content is shown. 
    + This is because we want the user to consciously perform an extra step before he assigns extract rights on a file.

* If the user has extract rights, they are shown the icon to perform the extraction. This results in an unprotected file being made available to the user. This unprotected file is not managed by SkyDRM. Changes made to the extracted file will not be in sync with the origin NXL file in SkyDRM.
  
## Icon
  
The image below shows the icon to be used.  
  
![NXL-Extract-100x100@3x.png](https://bitbucket.org/repo/dBgzdj/images/3662021261-NXL-Extract-100x100@3x.png)  
  
## Web app screens  
  
The following screens show the file listing and info.  
  
### Ad-hoc files   
   
Adding an ad-hoc file - more options.  
![31.png](https://bitbucket.org/repo/dBgzdj/images/424669173-31.png)  
  
Clicking on more options shows the "Extract" right. 
![32.png](https://bitbucket.org/repo/dBgzdj/images/3252173136-32.png)  
  
![33.png](https://bitbucket.org/repo/dBgzdj/images/1766475393-33.png)
  
The file is uploaded.
![34.png](https://bitbucket.org/repo/dBgzdj/images/4078174825-34.png)  

The icon showing the extract rights  
![35.png](https://bitbucket.org/repo/dBgzdj/images/4174270794-35.png)
    
The extract rights in the info window. 
![36.png](https://bitbucket.org/repo/dBgzdj/images/3253793906-36.png)
    
Clicking on the extract icon in the file listing downloads the extracted content to your hard disk. 
![37.png](https://bitbucket.org/repo/dBgzdj/images/2788829440-37.png)  
    
Windows explorer showing the extracted file. 
![38.png](https://bitbucket.org/repo/dBgzdj/images/2104158112-38.png) 
  
The downloaded file can be opened in a viewer. 
![39.png](https://bitbucket.org/repo/dBgzdj/images/3105258550-39.png)
    
### Central policy 
   
Creating a policy to enable "Extract" rights  
![20.png](https://bitbucket.org/repo/dBgzdj/images/2078682016-20.png)    
  
![21.png](https://bitbucket.org/repo/dBgzdj/images/1297874556-21.png)  
  
![22.png](https://bitbucket.org/repo/dBgzdj/images/3231042867-22.png)

## ToDo  
  
Screenshots / mockups for central policy protected files should be uploaded here: (a) file listing, (b) view info. These screens for central policy protected files would look identical to the screens for files protected with user defined rights.
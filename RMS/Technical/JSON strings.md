# Decision on handling JSON strings from NXL headers 
  
## Background  
  
  
There are two cases to be managed:  
  
* The [reclassify APIs](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Projects%20REST%20API#markdown-header-reclassify-file) and the [token APIs](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Token%20REST%20API) require the file tags. A checksum is computed **on the server** for the file tags. Each DUID is associated with a checksum. This checksum is used during the [retrieve decryption token](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Token%20REST%20API#markdown-header-retrieve-decryption-token) call to ensure the file header has not been tampered with. This is different than the MAC checksum stored in the NXL header described below.    

* Each section in the NXL header has a HMAC-SHA256 message authentication code that is computed and stored in the NXL header. The pseudocode for this computation is in the [section checksum](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMD/nxl.format.v2.md) link.   
  
The discussion with the teams raised two points:   
  
1. When client-side and server-side independently update the headers of their clones of the same NXL file, should they use equivalent JSON strings that are of identical formatting? (They should at least use equivalent JSON strings, of course.)  

2. Regardless of decision #1 above, should all equivalent JSON strings generate the same checksum for the purpose of Token RESP API call checking? (NXL file header itself also contains some checksums that are calculated some other way, but those are outside the scope of this discussion.)    
  
## Decision   
  
It was decided that the JSON strings created on clients and the server for the FilePolicy or FileTag sections would need to be identical. This is to enable the first point above, i.e., calculating the checksum on the string. This means formatting characters such as "\r\n" or other whitespace characters that do not affect the interpretation of the JSON object itself will cause a change in the checksum calculation as they are treated as strings. 
  
The same decision applies to the [retrieve token](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Token%20REST%20API#markdown-header-retrieve-decryption-token) API call since the checksum computation is identical to the description above, i.e., we do not parse the string into JSON, we compute the checksum on the string.   
  
This implies that the order of JSON tags also affects the checksum calculation. Tag order should be maintained. Following this protocol resolves both issues above. 

-- END --
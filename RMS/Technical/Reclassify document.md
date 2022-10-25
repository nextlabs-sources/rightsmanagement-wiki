## Issue description:

* People can make mistakes to select wrong classifications when they protect a file. We need to have a way to reclassify a document after it's encrypted.
## Questions:
1. Who should be allowed to change classifications? file owner or based on rights controlled by file policy?
2. How to do synchronization on different clones of files? If an in-place protected file has multiple clones, how to make sure the other clones are updated if I just modify the classification in one file.
3. Should the way to reclassify a document applicable to ad-hoc files as well? Means I can change the adhoc rights on a file if I am a owner or I have reclassify right on the file.

## Proposed Solution:
1. As per discussed with RMS-DEV team, we can control the admission to reclassify a file base on file rights. A reclassify right will be created.
2. It's hard to do sync up on the files for the in-place protection use case. The file is not uploaded to a central place, so different clients can not do synchronization on the changed file easily. So we propose to keep the latest change of classification only. When changing the classifications/adhoc rights, client need to call UpdateFileTags/UpdateFilePolicy API as proposed in [tokenManagement change proposal](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/Technical/Project%20TokenMgmt%20Proposal), so the checksums of fileTag/filePolicy will be updated in nxl_metadata table.

3. Not for adhoc-rights
4. Project upload API needs to be updated, before updating the file in repository, need check the classifications/file policy of the uploaded file, if the checksum is not matching with the latest checksum in nxl_metadata table, should reject to upload file.
# Cause of 'document deleted by steward' error when accessed in MyDrive

**Issue:**  The user clicks a file (He had uploaded the file before in his MyDrive. He is the document steward of that file) in his MyDrive. The expectation is that he should be able to view the file. However, the viewer gives the error 'The file you are trying to view is deleted by the document steward'

**Reason:** When he had uploaded the file in his MyDrive, it is actually stored in S3 bucket A. At some point, he changed the S3 appID and app secret (S3 credentials). Now when he uploads, all his files are stored in S3 bucket B.

Therefore, when he tries to access his old files, which are in bucket A, he will not be able to do so as his files are pointing to bucket B.   
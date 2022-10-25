**Scenario:**

1. Project admin sets the document classification as Project with these possible values: p01, p02, p03, p04 (multi-select enabled)

2. User uploads a file (file.nxl) to the project with company-defined rights. User selects p04 as the value. Nxl-header will have p04

3. After a few days, project admin modifies the document classification configuration. project now can only hold p01 and p02 as values

4. User tries to reclassify file.nxl. Modify rights template shows p01 and p02. Should p04 be shown or not?

**Decision:** Show p01 and p02. p04 wont be shown. A warning will be shown saying that the file has old classification values, which is invalid. saving it will remove the old values. 

**Decision taken by:** RMS-dev team

**Date:** 14th March 2019
# Cloud-Formation-Repo
An assignment repo focusing on the concepts of three tier architectures and using S3 as a data store for static web pages.

1a:
A static website was created based on this Tutorial:
https://docs.aws.amazon.com/AmazonS3/latest/userguide/HostingWebsiteOnS3Setup.html

The implementation follows the S3 Static Website Hosting via CloudFormation tutorial but was simplified to use a basic HTML/CSS website rather than Angular. 
Additional resource tags, a custom bucket name, and an error page were added to improve resource management and usability.
Versioning was enabled on the S3 bucket to maintain previous versions of website files.


Using:
Amazon S3
AWS CloudFormation
HTML
CSS

LLM promts used through CoPilot:
"Write a basic HTML/CSS website displaying a heading and a paragraph" - Pages were then edited to add my details and a error page was also added.
"Suggest tags for an S3 bucket hosting a static website." - Bit of an overkill of tags suggested, so cut it back to ones I thought important.


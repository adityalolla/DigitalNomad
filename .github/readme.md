#### Need to add to front end ci 

Cloudfront cache invalidation 

* aws cloudfront create-invalidation --distribution-id USE_GIT_SECRETS --paths /index.html
* aws cloudfront get-invalidation --distribution-id USE_GIT_SECRETS --id IDFROMFIRSTCOMMAND 

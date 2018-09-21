---
layout: post
date: 2015-12-30 15:54:33 GMT
tags:
- aws
- amazon web services
- s3
- copy
- aws cli
title: "Copying an S3 Bucket"
---
<p>This is how you copy the contents of an S3 bucket to a bucket in another AWS account.</p><figure class="tmblr-full" data-orig-height="600" data-orig-width="800"><img src="/images/aed84bc6ddb63bd7825d90a9adc16223b46fcab114b4cef9c1b246d2e221c179.jpg" data-orig-height="600" data-orig-width="800"></figure><h2>Install the AWS CLI</h2><p>If you’re on OS X like me, <a href="http://docs.aws.amazon.com/cli/latest/userguide/installing.html">installing the AWS CLI</a> is straightforward:</p><pre>$ curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip"<br>$ unzip awscli-bundle.zip<br>$ sudo ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws</pre><p>Open a new Terminal window or tab, and you can now use it.</p><h2>Store credentials</h2><p><a href="http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html#cli-config-files">Create a file</a> called credentials in ~/.aws that looks like:</p><pre>[default]<br>aws_access_key_id=YOUR_ACCESS_KEY_ID<br>aws_secret_access_key=YOUR_SECRET_ACCESS_KEY</pre><h2>Grant Permissions</h2><p>The account that owns the destination S3 bucket needs List and View permissions for the source S3 bucket. If your case is like mine, and your bucket only contains public content anyway, you can just add these permissions for everyone. Otherwise follow <a href="https://aws.amazon.com/premiumsupport/knowledge-center/account-transfer-s3/">these instructions</a>!</p><h2>Copy Recursively</h2><p>Now all that’s left is to use the AWS CLI to <a href="http://docs.aws.amazon.com/cli/latest/reference/s3/cp.htmla">recursively copy the objects</a> in the source bucket.</p><pre>aws s3 cp s3://sourceBucketName/ s3://destinationBucketName/ --recursive</pre><p>That’s about it! Doing something interesting on AWS? <a href="twitter.com/arpith">Let’s</a> <a href="arpith@feedreader.co">talk</a>!</p>
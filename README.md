# boxes.nickcharlton.net

This repo holds the site and configuration for http://boxes.nickcharlton.net/. This
is an S3 hosted static site which holds the generated output from [boxes][] by
providing a simple file listing.

It's based upon [Rufus Pollock][]'s [s3-file-listing][] example.

## Configuring S3

This is mostly configured along the lines of a typical static S3 site (you may find
[Amazon's documentation helpful][static-site-docs]). But, in brief:

1. Create a bucket after your intended domain (in this case: boxes.nickcharlton.net).
2. Enable for static site usage.
    - you should set the index page to: `index.html`.
3. Configure the permissions through a bucket policy.
4. Configure CORS so that the JavaScript can be used.
5. Set a DNS entry.
    - a `CNAME` pointing from `boxes.nickcharlton.net` to `boxes.nickcharlton.net.s3-website-us-west-2.amazonaws.com`

### Bucket Policy

There's two permissions which need to be set. The first allows `Everyone` to fetch
objects from the bucket. The next configures the bucket itself to allow file 
listing (you can see the distinction under the `Resource` attribute).

An example:

```json
{
    "Version": "2008-10-17",
    "Id": "Policy1383484446097",
    "Statement": [
        {
            "Sid": "Stmt1383484422944",
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::boxes.nickcharlton.net/*"
        },
        {
            "Sid": "Stmt1383484444634",
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:::boxes.nickcharlton.net"
        }
    ]
}
```

### CORS (Cross Origin Resource Sharing)

The trick here is to configure an additional `CORSRule` to allow the domain
(configured above) to allow `GET` requests. An example:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<CORSConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <CORSRule>
        <AllowedOrigin>*</AllowedOrigin>
        <AllowedMethod>GET</AllowedMethod>
        <MaxAgeSeconds>3000</MaxAgeSeconds>
        <AllowedHeader>Authorization</AllowedHeader>
    </CORSRule>
    <CORSRule>
        <AllowedOrigin>http://boxes.nickcharlton.net</AllowedOrigin>
        <AllowedMethod>GET</AllowedMethod>
    </CORSRule>
</CORSConfiguration>
```

I'd also recommend skim reading the [CORS S3][] documentation.

### Author / License

Copyright (c) Nick Charlton 2013. Licensed under the MIT license.

[boxes]: https://github.com/nickcharlton/boxes
[Rufus Pollock]: http://rufuspollock.org/
[s3-file-listing]: https://github.com/rgrp/s3-bucket-listing
[static-site-docs]: http://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteHosting.html
[CORS S3]: http://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html


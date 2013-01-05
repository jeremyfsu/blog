--- 
layout: post
title: Simple S3 backup for my EC2 instance
created: 1304702891
---
For disaster recovery, I have my HairForecast.com EC2 instance rsync'ing dumps of the SQL db to my home server nightly.  Between that, my own EC2 AMI, and SVN, I can have HairForecast up and running in a matter of minutes if something were to happen to it's primary EC2 instance. 

This is good, until this week while away from home, my homeserver went down for unknown reasons.  I'm getting nightly emails from my instance that it can't rsync to home. So I thought, now is a good time to just have it back up the files to S3.

I grabbed the Ruby S3 gem, and literally 5 minutes later I had this script locked and loaded to run tonight:

<pre>
#!/usr/bin/ruby
require 'aws/s3'
AWS::S3::Base.establish_connection!(
    :access_key_id     => 'ABCD',
    :secret_access_key => '1234'  )

AWS::S3::S3Object.store(ARGV[1], open(ARGV[2]), ARGV[0])
</pre>

Note that it takes 3 arguments, bucket name, filename, path to file.  I like to use absolute paths when running crons, since they never run where you expect them to. If you specify a path as a bucket key, then S3 will make folders in your bucket, which is cool I think.  But in this case I just wanted my backup files in the root of the bucket.

I'm delighted with the fact that it took longer to blog about this then it did to implement.

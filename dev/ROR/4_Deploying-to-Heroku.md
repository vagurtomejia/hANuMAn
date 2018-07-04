Ephemeral filesystem
---------------------
Each dyno gets its own ephemeral filesystem, with a fresh copy of the most recently deployed code. During the dyno’s lifetime its running processes can use the filesystem as a temporary scratchpad, but no files that are written are visible to processes in any other dyno and any files written will be discarded the moment the dyno is stopped or restarted. For example, this occurs any time a dyno is replaced due to application deployment and approximately once a day as part of normal dyno management.

AWS Simple Storage Service, e.g. S3, is a “highly durable and available store” and can be used to reliably store application content such as media files, static assets and user uploads. It allows you to offload your entire storage infrastructure and offers better scalability, reliability, and speed than just storing files on the filesystem.

AWS S3, or similar storage services, are important when architecting applications for scale and are a perfect complement to Heroku’s ephemeral filesystem.
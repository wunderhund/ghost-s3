# ghost-s3
Repo for storing a Dockerfile that runs the Ghost CMS with an S3 storage adapter installed.

## Intro:
The official Ghost Docker image, available from [here](https://hub.docker.com/_/ghost), doesn't come with the ability to connect to AWS S3 storage built-in. This Dockerfile installs [ghost-storage-adapter-s3](https://github.com/colinmeinke/ghost-storage-adapter-s3), which allows Ghost to connect to an S3 bucket for storing its `content/` folder. This is especially useful for those building a custom frontend with Ghost as the backend CMS.

## To Use:
This project uses [Docker](https://docs.docker.com/get-docker/).

You will need an AWS S3 bucket and an IAM user with permissions to use that bucket. Instructions for setting up your S3 bucket and IAM user can be found [here](https://github.com/colinmeinke/ghost-storage-adapter-s3#aws-configuration).

I've put this image up [on Dockerhub](https://hub.docker.com/repository/docker/wunderhund/ghost-s3), so you can pull it with: 
```
docker pull wunderhund/ghost-s3
```

To run from command line:
```
docker run -p 2368:2368 \
  -e storage__active=s3 \
  -e storage__s3__accessKeyId=<AWS Access Key> \
  -e storage__s3__secretAccessKey=<AWS Secret Key> \
  -e storage__s3__bucket=<s3 bucket name> \
  -e storage__s3__region=us-east-1 \
  wunderhund/ghost-s3:latest
```

I've also provided a sample docker-compose for running Ghost with S3 and mysql:
* Edit `docker-compose.yml` and put in your own values for the AWS IAM user and S3 bucket.
* Save the changes to `docker-compose.yml` and then run:
`docker-compose up --build`.

Getting this working was a little frustrating, so I figured I'd publish the results for everyone's convenience. The problem is that the `ghost-storage-adapter-s3` install instructions specify it neds to be moved to the `content/` directory, but the `storage__active` environment variable tells Ghost to look for that directory in S3, which it can't reach without the adapter! Special thanks to @mason for figuring out the trick of putting the adapter in the `content.orig/` directory instead:
https://github.com/docker-library/ghost/issues/195#issuecomment-604754501
page_title: Sharing data between 2 couchdb databases
page_description: Sharing data between 2 couchdb databases
page_keywords: docker, example, package installation, networking, couchdb, data volumes

# CouchDB Service

> **Note**: 
> 
> - This example assumes you have Docker running in daemon mode. For
>   more information please see [*Check your Docker
>   install*](../hello_world/#running-examples).
> - **If you don't like sudo** then see [*Giving non-root
>   access*](/installation/binaries/#dockergroup)

Here's an example of using data volumes to share the same data between
two CouchDB containers. This could be used for hot upgrades, testing
different versions of CouchDB on the same data, etc.

## Create first database

Note that we're marking `/var/lib/couchdb` as a data volume.

    COUCH1=$(sudo docker run -d -p 5984 -v /var/lib/couchdb shykes/couchdb:2013-05-03)

## Add data to the first database

We're assuming your Docker host is reachable at `localhost`. If not,
replace `localhost` with the public IP of your Docker host.

    HOST=localhost
    URL="http://$HOST:$(sudo docker port $COUCH1 5984 | grep -Po '\d+$')/_utils/"
    echo "Navigate to $URL in your browser, and use the couch interface to add data"

## Create second database

This time, we're requesting shared access to `$COUCH1`'s volumes.

    COUCH2=$(sudo docker run -d -p 5984 --volumes-from $COUCH1 shykes/couchdb:2013-05-03)

## Browse data on the second database

    HOST=localhost
    URL="http://$HOST:$(sudo docker port $COUCH2 5984 | grep -Po '\d+$')/_utils/"
    echo "Navigate to $URL in your browser. You should see the same data as in the first database"'!'

Congratulations, you are now running two Couchdb containers, completely
isolated from each other *except* for their data.

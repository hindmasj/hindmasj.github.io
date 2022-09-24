# Some Redis Notes

[Home](../index) | [Up](misc-index)

## Reading

* [Redis Site](https://redis.io/)
* [Jedis](https://github.com/redis/jedis): ([QuickStart](https://github.com/redis/jedis/blob/master/docs/redisjson.md), [Maven](https://github.com/redis/jedis/blob/master/docs/jedis-maven.md))

# Docker

To have a play with Redis.

## Start Docker Container

Use the [Bitnami](https://bitnami.com/stack/redis/containers) images.

```
docker run --env REDIS_PASSWORD=password --rm -d --name redis -p 6379:6379 bitnami/redis:latest
```

## Remote Client

Then do a quick connection to the exposed port.

```
nc localhost 6379
auth password
+OK
set fred "hello world"
+OK
set bill "how are you"
+OK
get fred
$11
hello world
get bill
$11
how are you
^C
```

## Command Line Client

Or run the command line from the container.

```
docker exec -it redis redis-cli --pass password
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
127.0.0.1:6379> get fred
"hello world"
127.0.0.1:6379> get bill
"how are you"
127.0.0.1:6379>
```

# A Thought On Indexing IPv4 CIDR Keys

Say you want to store some information about an IPv4 CIDR. Maybe I have a busy web site and I want to know more about all my visitors, so I look up their GeoIP data. So I set up a database with all the GeoIP data I can get hold of. Each entry is a CIDR of some sort.

```
SET 192.168.0.0/16 '{"city":"foo","country":"bar","isp":"bingo.bongo.org"}'
```

Then when you get an event containing an IP address, and you want to look up the information about it, how do you create the right key if you do not know the CIDR range the information is stored under.

## Step Through Possibilities Until You Get A Match

So the brute force method would be to start generating keys starting from the lowest scope CIDR and searching for them until you find a match. So start with "/32" and keep going.

```
GET 192.168.128.127/32
(nil)
GET 192.168.128.126/31
(nil)
... yawn, yawn ...
GET 192.168.128.0/17
(nil)
GET 192.168.0.0/16
"{\"city\":\"foo\",\"country\":\"bar\",\"isp\":\"bingo.bongo.org\"}"
```

But in this case we take 17 searches to find our answer, this seems inefficient.

## Create Links To The Real Key And Do A 2-Step Search

This method involves more effort up front, but speeds up the searches, so every search only requires 2 searches at most.

First, generate a set of records for each address in the CIDR range that returns the real key. Here we denote a link record with a '#'.

```
SET 192.168.0.0/32 '#192.168.0.0/16'
SET 192.168.0.1/32 '#192.168.0.0/16'
...
SET 192.168.128.127/32 '#192.168.0.0/16'
...
SET 192.168.255.255/32 '#192.168.0.0/16'
```

Then when you do the search, if the result that comes back starts with a hash, then use the rest of the result as the key you need to get the real data.

```
GET 192.168.128.127/32
"#192.168.0.0/16"

GET 192.168.0.0/16
"{\"city\":\"foo\",\"country\":\"bar\",\"isp\":\"bingo.bongo.org\"}"
```

Note each key is a CIDR, just in case the record is attached to a single address.

So this method speeds up searches at the expense of needing more storage space, but not as much space as if you just exploded the records so that there was a full record for each address in the range.

---
[Home](../index) | [Up](misc-index)

# PutBucketLogging {#reference_t1g_zj5_tdb .reference}

Provided with PutBucketLogging, bucket owners can enable the access logging function for their bucket. 

When this function is enabled, OSS automatically records the details about the requests to this bucket, and follows the user-specified rules to write the access logs as an object into a user-specified bucket on an hourly basis. OSS provides bucket access logs for bucket owners to understand and analyze bucket access behaviors easily.  The bucket access logs provided by OSS do not guarantee that every single access record is logged.

**Note:** OSS provides bucket access logs for bucket owners to understand and analyze bucket access behaviors easily.  The bucket access logs provided by OSS do not guarantee that every single access record is logged.

## Request syntax {#section_rfs_rnr_bz .section}

```
PUT /? logging HTTP/1.1
Date: GMT Date
Content-Length：ContentLength
Content-Type: application/xml
Authorization: SignatureValue 
Host: BucketName.oss-cn-hangzhou.aliyuncs.com
<? xml version="1.0" encoding="UTF-8"? >
<BucketLoggingStatus>
    <LoggingEnabled>
        <TargetBucket>TargetBucket</TargetBucket>
        <TargetPrefix>TargetPrefix</TargetPrefix>
    </LoggingEnabled>
</BucketLoggingStatus>
```

## Request elements {#section_dwh_vnr_bz .section}

|Name|Type|Required|Description|
|----|----|--------|-----------|
|BucketLoggingStatus|container|Yes|The container for storing access log status informationChild element: LoggingEnabled

Parent element: none

|
|LoggingEnabled|container|No|The container for storing access log information.  This element is required only when server access logging is enabled. Child element: TargetBucket,  TargetPrefix

Parent element: BucketLoggingStatus

|
|TargetBucket|character|This element is required when server access logging is enabled|The bucket for storing access logs. Child element: none

Parent element: BucketLoggingStatus.LoggingEnabled

|
|TargetPrefix|character|No|The prefix of the names of saved access log files.  Type: Child element: none Parent element: BucketLoggingStatus.LoggingEnabled|

## Naming rules for the objects storing access logs {#section_kv3_ynr_bz .section}

```
<TargetPrefix><SourceBucket>-YYYY-mm-DD-HH-MM-SS-UniqueString
```

In the naming rules, the TargetPrefix is specified by the user; YYYY, mm, DD, HH,  MM, and SS give the year, month, day, hour, minutes, and seconds of the creation time in Arabic numerals \(note the digits\); and UniqueString is the string generated by OSS system.  An example for the name of an object actually used to store OSS access logs is given as follows:

```
MyLog-oss-example-2012-09-10-04-00-00-0000
```

In the preceding example, “MyLog-“ is the Object prefix specified by the user; “oss-example” is the name of the origin bucket; “2012-09-10-04-00-00” is the Object creation time \(Beijing time\); and “0000”  is the string generated by OSS system.

## Log file format {#section_ugs_b4r_bz .section}

|Name|Example|Description|
|----|-------|-----------|
|Remote IP|119.140.142.11|IP address from which the request is initiated \(the proxy or user firewall may block this field\)|
|Reserved|-|Reserved field|
|Reserved|-|Reserved field|
|Time|\[02/May/2012:00:00:04 +0800\]|Time when OSS receives the request|
|Request-URI|“GET /aliyun-logo.png HTTP/1.1“|User-Requested URI \(including query-string\)|
|HTTP Status|200|HTTP status code returned by OSS|
|SentBytes|5576|Traffic that the user downloads from OSS|
|RequestTime \(ms\)|71|Time utilized in completing this request \(in ms\)|
|Referer| ```
http://www.aliyun.com/product/oss
```

 |HTTP Referer in the request|
|User-Agent|curl/7.15.5|HTTP User-Agent header|
|HostName|oss-example.regionid.example.com|Domain name for access request|
|Request ID|505B01695037C2AF032593A4|UUID used to uniquely identify this request|
|LoggingFlag|true|Whether the access logging function is enabled|
|Requester Aliyun ID|1657136103983691|Alibaba Cloud ID of the requester, “-“ for an anonymous access|
|Operation|GetObject|Request type|
|Bucket|oss-example|Name of the bucket requested for access|
|Key|/aliyun-logo.png|Key of user request|
|ObjectSize|5576|Object size|
|Server Cost Time \(ms\)|17|Time utilized by OSS server to process this request \(in ms\)|
|Error Code|NoSuchBucket|Error code returned by OSS|
|Request Length|302|Length of user request \(byte\)|
|UserID|1657136103983691| ID of the bucket owner|
|Delta DataSize|280|Bucket size variation, “-“ for no change|
|Sync Request|-|Whether this is an origin retrieval request from CND, “-“ for no|
|Reserved|-|Reserved field|

## Detail analysis {#section_zdp_24r_bz .section}

-   The source bucket and target bucket must belong to the same user.
-   In the preceding request syntax, “BucketName” refers to the bucket for which access logging is enabled; “TargetBucket” refers to the bucket into which access logs are saved; “TargetPrefix” refers to the name prefix of the object storing access logs and can be null.
-   The source bucket and target bucket can be the same or different buckets. You can save logs from multiple source buckets to the same target bucket \(in this case, we recommend that you assign different values to TargetPrefix\).
-   To disable the access logging function for a bucket, you only must send an empty BucketLoggingStatus request. For a detailed method, see the following request example.
-   All PUT Bucket Logging requests must be provided with signatures, because the anonymous access is not supported.
-   If the initiator of a PUT Bucket Logging request is not the owner of the source bucket \(BucketName in the request example\), OSS returns error code 403.
-   If the source bucket does not exist, OSS returns the error code: NoSuchBucket.
-   If the initiator of a PUT Bucket Logging request is not the owner of the target bucket \(indicated by TargetBucket in the request example\), OSS returns Error 403. If the target bucket does not exist, OSS returns the error code: InvalidTargetBucketForLogging.
-   The source bucket and target bucket must belong to the same data center. Otherwise, Error 400 with the error code: InvalidTargetBucketForLogging is returned.
-   If a PUT Bucket Logging request has an invalid XML, the error code: MalformedXML is returned.
-   The source bucket and target bucket can be the same bucket. You can save the logs of different source buckets into the same target bucket \(note that you must set TargetPrefix to different values\).
-   When the source bucket is deleted, the corresponding logging rules are also deleted.
-   OSS generates a bucket access log file every hour. However, all requests during the hour may not be recorded in the log file, but may get recorded in the previous or next log file.
-   In the naming rules for log files generated by OSS, “UniqueString” is only a UUID that OSS generates for a file to uniquely identify the file.
-   Each time OSS generates a bucket access log file, this is considered a PUT operation and the occupied space is recorded, but the generated traffic is not recorded.  After log files are generated, you can operate these log files as common objects.
-   OSS ignores all query-string parameters prefixed by “x-“ but such query-string parameters are recorded in access logs.  If you want to mark a special request from massive access logs, you can add a query-string parameter prefixed by “x-“ to the URL.  For example:

    `http://oss-example.regionid.example.com/aliyun-logo.png`

    `http://oss-example.regionid.example.com/aliyun-logo.png?x-user=admin`

    When OSS processes the preceding two requests, the results are the same. However, you can search access logs with “x-user=admin” to quickly locate the marked request.

-   You may see “-“ in any field of OSS logs. It indicates that data is unknown or the field is invalid for the current request.
-   Certain fields are added to the end of OSS log files in future based on the requirements. We recommend that developers to consider compatibility issues when developing log processing tools.
-   If you have uploaded the Content-MD5 request header, OSS calculates the body’s Content-MD5 and checks if the two are the same. If the two are different, the error code: InvalidDigest is returned.

## Example {#section_byq_m4r_bz .section}

**Example of a request for enabling bucket access logging:**

```
PUT /? logging HTTP/1.1
Host: oss-example.oss-cn-hangzhou.aliyuncs.com
Content-Length: 186
Date: Fri, 04 May 2012 03:21:12 GMT
Authorization: OSS qn6qrrqxo2oawuk53otfjbyc:KU5h8YMUC78M30dXqf3JxrTZHiA=
<? xml version="1.0" encoding="UTF-8"? >
<BucketLoggingStatus>
<LoggingEnabled>
<TargetBucket>doc-log</TargetBucket>
<TargetPrefix>MyLog-</TargetPrefix>
</LoggingEnabled>
</BucketLoggingStatus>
```

**Response example:**

```
HTTP/1.1 200 OK
x-oss-request-id: 534B371674E88A4D8906008B
Date: Fri, 04 May 2012 03:21:12 GMT
Content-Length: 0
Connection: keep-alive
Server: AliyunOSS
```

**Example of a request for disabling bucket access logging:**

```
PUT /? logging HTTP/1.1
Host: oss-example.oss-cn-hangzhou.aliyuncs.com
Content-Type: application/xml
Content-Length: 86
Date: Fri, 04 May 2012 04:21:12 GMT
Authorization: OSS qn6qrrqxo2oawuk53otfjbyc:KU5h8YMUC78M30dXqf3JxrTZHiA=
<? xml version="1.0" encoding="UTF-8"? >
<BucketLoggingStatus>
</BucketLoggingStatus>
```

**Response example:**

```
HTTP/1.1 200 OK
x-oss-request-id: 534B371674E88A4D8906008B
Date: Fri, 04 May 2012 04:21:12 GMT
Content-Length: 0
Connection: keep-alive
Server: AliyunOSS
```


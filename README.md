# Lambda http check

Lambda function to check specific http endpoint, and report on it's availability.


Optionally, it can record metrics to CloudWatch.

## Inputs

All inputs are either defined as environment variables or as part of event data. Event data
will take priority over environment variables

`ENDPOINT` - url to be checked

`METHOD` - http method to use, defaults to `GET`

`PAYLOAD` - http payload, if `POST` or `PUT` used as method

`TIMEOUT` - timeout to use for http requests, defaults to 120s

`HEADERS` - list of headers to send to target server, defaults to empty list.
Headers should be specified in

`REPORT_RESPONSE_BODY` - set to 1 if you wish to report on response body, 0
otherwise, 0 otherwise, defaults to 0

`REPORT_AS_CW_METRICS` - set to 1 if you wish to store reported data as CW
custom metrics, 0 otherwise, defaults to 1

`CW_METRICS_NAMESPACE` - if CW custom metrics are being reported, this will determine
their namespace, defaults to 'HttpCheck'



## Outputs

By default, following properties will be rendered in output Json

`Reason` - Reason

`Available` - 0 or 1

`TimeTaken` - Time in ms it took to get response from remote server. Default timeout
is 2 minutes for http requests.

`StatusCode` - Http Status Code

`ResponseBody` - Optional, by default this won't be reported


## Deployment

You can either deploy Lambda manually, or through [serverless](serverless.com) project.
If serverless is being chosen as method of deployments use command below, while
making sure that you have setup proper access keys. For more information [read here](https://serverless.com/framework/docs/providers/aws/guide/workflow/)

Serverless framework version used during development
is `1.23.0`, but it is very likely that later versions
will function as well

```
sls deploy
```

If you are setting up your Lambda function by hand, make sure it has proper IAM
permissions to push Cloud Watch metrics data, and to write to CloudWatch logs

## Testing

To test function locally with simple Google url (default), run following

```
sls invoke local  -f httpcheck
```

Optionally, for complicated example take a look at `test/ipify.json` file

```
$ sls invoke local  -f httpcheck -p test/ipifytimeout.json 
Failed to connect to https://api.ipify.org?format=json
<urlopen error _ssl.c:732: The handshake operation timed out>
Failed to publish metrics to CloudWatch:'TimeTaken'
Result of checking https://api.ipify.org?format=json
{
 "Available": 0,
 "Reason": "<urlopen error _ssl.c:732: The handshake operation timed out>"
}
{
    "Available": 0,
    "Reason": "<urlopen error _ssl.c:732: The handshake operation timed out>"
}
```

## Debugging

If you wish to see debug output for http request, set `HTTP_DEBUG` environment
variable to '1'. This can't be controlled through event payload. 

## Schedule execution 

Pull requests are welcome to serverless project to deploy CloudWatch rules in order
to schedule execution of Http Checking Lambda function. 
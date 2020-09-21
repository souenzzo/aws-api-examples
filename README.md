# AWS API Examples

Some tips about use [aws-api](https://github.com/cognitect-labs/aws-api)




## Send a email 

```clojure
(aws/invoke
  sesv2
  {:op      :SendEmail
   :request {:Destination      {:ToAddresses ["to@example.com"]}
             :FromEmailAddress "from@example.com"
             :Content          {:Simple {:Subject {:Data    "hello"
                                                   :Charset "UTF-8"}
                                         :Body    {:Text {:Data    "world"
                                                          :Charset "UTF-8"}}}}}})
=> {:MessageId "00000172bd250f73-50682c83-a6b3-4086-909c-064859a10cc9-000000"}
```

### Common issues

```clojure
=> {:message "Email address is not verified. The following identities failed the check in region US-EAST-1: to@example.com",
    :cognitect.anomalies/category :cognitect.anomalies/incorrect}
```

- Both `:FromEmailAddress` / `:ToAddresses` needs to be verified in `Simple Email Service > Email Address`. Checkout "Verify a email"

```clojure
=> {:message nil, :cognitect.anomalies/category :cognitect.anomalies/incorrect}
```

- Probably missing `:FromEmailAddress` that isn't listed as `:required` but it is.

## Verify a email

```clojure
(aws/invoke
  sesv2
  {:op      :CreateEmailIdentity
   :request {:EmailIdentity "me@example.com"}})
=> {:IdentityType             "EMAIL_ADDRESS"
    :VerifiedForSendingStatus false
    :DkimAttributes           nil}
```

Then login into `me` and authorize AWS to send emails for you inside sandbox.


## Create a pull request in codecommit

```clojure
(aws/invoke codecommit
            {:op      :CreatePullRequest
             :request {:title   "Create a PR Example"
                       :targets [{:repositoryName  "aws-api-examples"
                                  :sourceReference "feature/pr-example"}]}})
```

## Get logs

`:logGroupName` you can get in `CloudWatch > CloudWatch Logs > Log groups`.

`:logStreamName` is usually dynamic, once applications scale/start/stop instances, it will change. My dev env only use once instance, so it's easy to get it.

```clojure
(aws/invoke logs
            {:op      :GetLogEvents
             :request {:logGroupName  "AppDev-HTTPLogGroup-XYZXYZXYZ"
                       :logStreamName "i-0202020202/my-awesome-app.log"}})
=> {:events [{:timestamp 1592222752489,
              :message   "09:05:47.508 INFO  org.eclipse.jetty.util.log - Logging initialized @118757ms to org.eclipse.jetty.util.log.Slf4jLog",
              :ingestionTime 1592222758746}]
 :nextForwardToken "f/35508359754442739897239510339078412739981807580925853699",
 :nextBackwardToken "b/35507753902560279538697240774210420589596513875306545152"}
```

## Send a message to SNS

(aws/invoke sns {:op      :Publish
                 :request {:TopicArn "arn:aws:sns:us-east-6:666666666:topic-name"
                           :Message "{\"hello\": \"world\", "this message is usually a": \"JSON!\"}"}})

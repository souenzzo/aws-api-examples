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
```

### Common issues

```clojure
=> {:message "Email address is not verified. The following identities failed the check in region US-EAST-1: to@example.com",
    :cognitect.anomalies/category :cognitect.anomalies/incorrect}
```

- Both `:FromEmailAddress` / `:ToAddresses` needs to be verified in `Simple Email Service > Email Address`

```clojure
=> {:message nil, :cognitect.anomalies/category :cognitect.anomalies/incorrect}
```

- Probably missing `:FromEmailAddress` that isn't listed as `:required` but it is.

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



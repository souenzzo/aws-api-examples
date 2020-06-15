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


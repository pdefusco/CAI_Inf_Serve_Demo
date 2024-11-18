# CAI_Inf_Serve_Demo

### Notebook Demos

### CLI Demo

List existing Serving Apps:

```
% cdp ml list-ml-serving-apps

# SAMPLE OUTPUT
{
    "apps": [
        {
            "cloudPlatform": "AWS",
            "appName": "go01-aws-ml-serving",
            "appCrn": "crn:cdp:ml:us-west-1:8a1e15cd-04c2-48aa-8f35-b4a8c11997d3:mlserving:dcbc7c73-642b-4479-a253-a0358470d6c5",
            "environmentCrn": "crn:cdp:environments:us-west-1:8a1e15cd-04c2-48aa-8f35-b4a8c11997d3:environment:76844378-f8bd-48c0-9567-f584f79a28d5",
            "environmentName": "go01-demo-aws",
            "ownerEmail": "akahan@cloudera.com",
            "mlServingVersion": "1.2.0-b73",
            "isPrivateCluster": false,
            "creationDate": "2024-10-16T15:44:49.768000+00:00",
            "cluster": {
                "clusterName": "ml-14b7f927-4cc",
                "domainName": "go01-aws-ml-serving.go01-dem.ylcu-atmi.cloudera.site",
                "liftieID": "liftie-0spym1jd",
                "isPublic": false,
                "ipAllowlist": "0.0.0.0/0",
                "authorizedIpRangesAllowList": false,
                "tags": [],
                "instanceGroups": [],
                "clusterCrn": "crn:cdp:compute:us-west-1:8a1e15cd-04c2-48aa-8f35-b4a8c11997d3:cluster:liftie-0spym1jd"
            },
            "status": "modify:finished",
            "usePublicLoadBalancer": true,
            "httpsEnabled": true,
            "subnetsForLoadBalancers": []
        }
    ]
}
```

Create the model specification for the selected model:

```
vi examples/mlflow/model-spec-llama.json


```

```
export DOMAIN=$(cdp ml describe-ml-serving-app --app-crn "crn:cdp:ml:us-west-1:8a1e15cd-04c2-48aa-8f35-b4a8c11997d3:mlserving:dcbc7c73-642b-4479-a253-a0358470d6c5" | jq -r '.app.cluster.domainName')
```

Generate CDP API Token:

```
CDP_TOKEN=$(cdp iam generate-workload-auth-token --workload-name DE | jq -r '.token')
```

Finally, deploy the model from the Registry:

```
curl -v  -H "Content-Type: application/json" -H "Authorization: Bearer ${CDP_TOKEN}" "https://${DOMAIN}/api/v1alpha1/deployEndpoint" -d @./examples/mlflow/model-spec-llama.json
```

List model endpoints:

```
curl -H "Content-Type: application/json" -H "Authorization: Bearer ${CDP_TOKEN}" "https://${DOMAIN}/api/v1alpha1/listEndpoints" -d '{"namespace": "serving-default"}'
```

Describe model endpoint:

```
curl -s -H "Authorization: Bearer ${CDP_TOKEN}" -H "Content-Type: application/json" https://${DOMAIN}/api/v1alpha1/describeEndpoint -d '{"namespace": "serving-default", "name": "llama-3-1-8b-instruct"}'|jq
```

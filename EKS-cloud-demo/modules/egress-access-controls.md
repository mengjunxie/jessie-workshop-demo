# Module 7: North-South Controls-Egress access controls

**Goal:** Configure egress access control for specific workloads so they are allow to external DNS domain.

## Steps

1. Implement DNS policy to allow the external endpoint access from a specific workload, e.g. `dev/centos`.

    a. Apply a policy to allow access to `api.twilio.com` endpoint using DNS rule.

    ```bash
    # deploy dns policy
    kubectl apply -f demo/20-egress-access-controls/dns-policy.yaml

    # test egress access to api.twilio.com
    kubectl -n dev exec -t centos -- sh -c 'curl -m3 -skI https://api.twilio.com 2>/dev/null | grep -i http'
    # test egress access to www.google.com
    kubectl -n dev exec -t centos -- sh -c 'curl -m3 -skI https://www.google.com 2>/dev/null | grep -i http'
    ```

    Access to the `api.twilio.com` endpoint should be allowed by the DNS policy and any other external endpoints like `www.google.com` should be denied. 

    b. Modify the policy to include `www.google.com` in dns policy and test egress access to www.google.com again.

    ```bash
    # test egress access to www.google.com again and it should be allowed.
    kubectl -n dev exec -t centos -- sh -c 'curl -m3 -skI https://www.google.com 2>/dev/null | grep -i http'
    ```


2.  Edit the policy to use a `NetworkSet` with DNS domain instead of inline DNS rule.

    a. Apply a policy to allow access to `api.twilio.com` endpoint using DNS policy.

    ```bash
    # deploy network set
    kubectl apply -f demo/20-egress-access-controls/netset.external-apis.yaml
    # deploy DNS policy using the network set
    kubectl apply -f demo/20-egress-access-controls/dns-policy.netset.yaml


    # test egress access to api.twilio.com
    kubectl -n dev exec -t centos -- sh -c 'curl -m3 -skI https://api.twilio.com 2>/dev/null | grep -i http'
    # test egress access to www.google.com
    kubectl -n dev exec -t centos -- sh -c 'curl -m3 -skI https://www.google.com 2>/dev/null | grep -i http'
    ```
    
    b. Modify the `NetworkSet` to include `www.google.com` in dns domain and test egress access to www.google.com again.

    ```bash
    # test egress access to www.google.com again and it should be allowed.
    kubectl -n dev exec -t centos -- sh -c 'curl -m3 -skI https://www.google.com 2>/dev/null | grep -i http'
    ```

3. Protect workloads from known bad actors.

    Calico offers `GlobalThreatfeed` resource to prevent known bad actors from accessing Kubernetes pods.

    ```bash
    # deploy feodo tracker threatfeed
    kubectl apply -f demo/10-security-controls/feodotracker.threatfeed.yaml
    # deploy network policy that uses the threadfeed
    kubectl apply -f demo/10-security-controls/feodo-block-policy.yaml

    #The ip block list from feodo
    https://feodotracker.abuse.ch/downloads/ipblocklist.txt

   
    ```

[Next -> Module 9](../modules/using-observability-tools.md)

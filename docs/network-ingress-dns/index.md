# Network Ingress and DNS

### Network Ingress

#### Ensure the cloud provider implementation has support for load balancers

If OpenShift should manage load balancers for this platform, provide load balancer
integration in the [load balancer service][load-balancer-service]

For example:
- add provider-specific annotations to `InternalLBAnnotations` and `managedLoadBalancerServiceAnnotations`
- evaluate customizations needed for load balancer service configuration in `desiredLoadBalancerService`
- add unit tests to catch regressions

#### Evaluate ingress provider-specific support for load balancers

Review the end-to-end tests in the [operator tests][operator-tests]
and make customizations, or skip the tests for this provider if they don't apply.

For example:
- see `TestProxyProtocolOnAWS` for an example for the AWS cloud provider 
- check if you require `TestInternalLoadBalancer`, `TestIngressControllerCustomEndpoints`, `TestLocalWithFallbackOverrideForLoadBalancerService` for your platform
- add unit tests to catch regressions

#### Evaluate provider-specific endpoint publishing strategy

Look through the doc at the [custom resource definition][crd]
to understand the details for `endpointPublishingStrategy`, which is the set of parameters
used to publish the ingress controller endpoints to other networks, enable load
balancer integrations, and other tasks.

Understand the properties:
- hostNetwork, loadBalancerService, nodePortService, private

Check if customizations are needed in the [ingress controller operator][ingress-controller-operator]

For example: 
- add your default strategy to `setDefaultPublishingStrategy`
- add your integration for `IsProxyProtocolNeeded`
- add unit tests to catch regressions

#### Document the default endpoint publishing strategy for the provider

### DNS

#### Evaluate provider-specific DNS support and validate the controller

Check if customizations are needed in the [DNS operator][dns-operator]

For example:
- add your platform type to `createDNSProvider`, and `createDNSProviderIfNeeded`
- add unit tests to catch regressions

#### Evaluate provider-specific externalDNS support (4.10+)

Starting in OpenShift 4.10, there is the External-DNS operator to consider.
It will support only these platforms in 4.10:
- AWS
- GCP
- Azure

#### Questions

Questions can be directed to the OpenShift Slack channel **#forum-network-edge**

[load-balancer-service]: https://github.com/openshift/cluster-ingress-operator/blob/master/pkg/operator/controller/ingress/load_balancer_service.go
[operator-tests]: https://github.com/openshift/cluster-ingress-operator/blob/master/test/e2e/operator_test.go
[crd]: https://github.com/openshift/cluster-ingress-operator/blob/master/manifests/00-custom-resource-definition.yaml
[ingress-controller-operator]: https://github.com/openshift/cluster-ingress-operator/blob/master/pkg/operator/controller/ingress/controller.go
[dns-operator]: https://github.com/openshift/cluster-ingress-operator/blob/master/pkg/operator/controller/dns/controller.go
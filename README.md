# uFlow

⚠️ **DRAFT** ⚠️

## Overview

`uFlow` stands for "user flow" and is a framework that mimics the behaviour of cloud native application users. The framework allows to easily create "external E2E" tests, in which an external program (outside the cluster) interacts with cloud native applications and systems to test them.

The goal of `uFlow` would be to test Kyma from a user perspective, effectively checking that users can do what they are supposed to and additionally eliminating any manual tests.

## Features

`uFlow` would have the following features built in to help write tests:
- Kyma login capabilities as any kind of user (customer, admin, SRE...).
- Kubectl capabilities as any kind of user.
- Builtin functions access to any Kyma APIs.
- Builtin functions to access Kyma resources.
- Builtin functions to do common actions in one line.
- Utilities to ingest, parse and assert Kyma's responses, configs, etc...

## Concept test case

Using `uFlow` as a client, Kyma developers would write Go tests in such a fashion:
```go
func TestCustomerFunction(t *testing.T) {
    // login to Kyma cluster as customer
    customer := uFlow.Login(customerCredentials)

    // create a namespace as customer
    ns := customer.CreateNamespace("customerA")

    // create a new test function
    f := customer.CreateFunc("my func code...", runtimeX, deps...)
    
    // trigger or call the function
    err := f.Trigger(event) // err := f.Expose("some.domain").Call()
    // check that it did run 
    assert.NoError(t, err)

    // collect function logs and check expected result
    logs := f.Logs()
    assert.Contains(t, "my expected log message", logs)

    // check that logs are pushed to the external Log Management System if there is one configured in the cluster
    if (env.LMS) {
        k8s := uFlow.NewKubeClient(kubeconfig)
        logs := k8s.LMS().Query("query to my namespace and function")
        assert.Contains(t, "my expected log message", logs)
    }
}
```

## Roadmap
- Ideally, this framework would make use of Hydroform to access the different kyma resources.
- First steps would be to try to automate some of the current manual test cases.
- Start small and build more hydroform modules and `uFlow` features as we need them.
- `uFlow` test cases should ideally also be runnable from a local machine against the desired cluster. -> Devs can check user flows

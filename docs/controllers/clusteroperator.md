# Cluster operator controller

## Overview

[Cluster operator controller](../../pkg/controllers/clusteroperator/clusteroperator_controller.go) is responsible for managing the CoreProvider and InfrastructureProvider CRs.
These CRs are later reconciled by the upstream [Cluster API Operator](https://github.com/kubernetes-sigs/cluster-api-operator).

## Behavior

```mermaid
stateDiagram-v2
    [*] --> ReadCoreProviderAsset
    ReadCoreProviderAsset --> CreateOrUpdateCoreProvider
    CreateOrUpdateCoreProvider --> SubstituteCoreProviderImage
    SubstituteCoreProviderImage --> IsCurrentPlatformSupported
    state IsCurrentPlatformSupported <<choice>>
    IsCurrentPlatformSupported --> ReadInfrastructureProviderAsset: True
    IsCurrentPlatformSupported --> NoOp: False
    ReadInfrastructureProviderAsset --> CreateOrUpdateInfrastructureProvider
    CreateOrUpdateInfrastructureProvider --> SubstituteInfrastructureProviderImage
    SubstituteInfrastructureProviderImage --> [*]
    NoOp --> [*]
```

Operator will create CoreProvider even if the current platform is not supported, this allows "bring your own" 
scenarios. If the platform is supported, the operator will create the appropriate InfrastructureProvider.

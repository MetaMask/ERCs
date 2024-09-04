---
eip: 7716
title: Wallet Interface for Saving Permissions
description: A standard interface for wallets to save permissions and issue delegation contexts
author: Dan Finlay (@danfinlay)
discussions-to: TBD
status: Draft
type: Standards Track
category: ERC
created: 2023-12-14
requires: 7710
---

## Abstract

This ERC proposes a standard wallet interface that allows dApps to request the wallet to save permissions in a way that is compatible with ERC-7710. The wallet can then expose those permissions to the user, and ideally expose a way to also re-grant them to a website.

The wallet can then issue a `permissionsContext` and `delegationManager` as defined in ERC-7710. This standard builds upon the concepts introduced in ERC-7715 and ERC-7710 to create a more unified and flexible permission management system for Ethereum wallets.

## Motivation

As decentralized applications (dApps) become more complex, there's an increasing need for wallets to manage permissions in a standardized way. While 7715 provides a way for dApps to request permissions from wallets, it does not provide a way for wallets to save permissions in a way that is compatible with 7710. This ERC aims to:

1. Provide a consistent method for dApps to save permissions to wallets.
2. Enable wallets to receive standardized delegation contexts that can be used across different dApps.
3. Improve user experience by reducing the need for frequent transaction signing.
4. Enhance security by allowing fine-grained control over permissions.

## Specification

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119.

### Wallet Interface

Wallets MUST implement the following two methods:

### `wallet_getDelegationTarget`

First we need to get a local address to delegate this to (an "app key"):
```typescript
const appKey = await provider.request({
  method: 'wallet_getDelegationTarget',
  params: [{
    chainId: '0x1', // EIP-155 chain ID as hex string
    // Alternatively, using CAIP-2 chain ID:
    // chainId: 'eip155:1'
  }]
});
```

### `wallet_savePermission`

We introduce a `wallet_savePermission` method for the DApp to request the Wallet to save permissions.

The application can then assign its permission to that address, and then save the permission:

```typescript
type Permission = {
  appKey: string;
  permissionsContext: string;
  delegationManager: string;
  expiration: number;
}

provider.request({
  method: 'wallet_savePermission',
  params: [permission]
});
```

## Rationale

This ERC builds upon ERC-7715 and ERC-7710 to create a more comprehensive and flexible permission management system. By allowing dApps to save permissions directly to wallets, we enable a more seamless user experience while maintaining security and control.

The `wallet_getDelegationTarget` method provides a way for dApps to obtain a local address specific to their application. This app-specific key allows for more granular permission management and reduces the risk of permission conflicts between different dApps.

The `wallet_savePermission` method enables dApps to store standardized permission objects in the wallet. This approach allows wallets to manage permissions consistently across different applications and provides users with a centralized view of all granted permissions.

## Backwards Compatibility

This ERC is designed to be compatible with existing wallet implementations. Wallets that do not support these new methods can simply return an error, allowing dApps to fall back to traditional transaction signing methods.

## Test Cases

(Test cases for an implementation are required for ERCs that are affecting consensus changes. Other ERCs can choose to include links to test cases if applicable.)

## Reference Implementation

(The implementations must be completed before any ERC is given status "Final", but it need not be completed before the ERC is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.)

## Security Considerations

1. Wallets MUST ensure that permissions are stored securely and cannot be accessed or modified by unauthorized parties.
2. Implementations SHOULD provide clear user interfaces for managing saved permissions, including the ability to view, edit, and revoke permissions.
3. Wallets SHOULD implement time-based or usage-based automatic revocation of permissions to limit the potential impact of compromised permissions.
4. DApps SHOULD request the minimum necessary permissions and duration required for their functionality.
5. Wallets MUST provide single-use keys in response to the `wallet_getDelegationTarget` method (so that it cannot be fooled into misusing the wrong permission).

## Copyright

Copyright and related rights waived via [CC0](../LICENSE.md).

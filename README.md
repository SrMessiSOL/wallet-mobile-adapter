# LazorKit React Native SDK

LazorKit allows you to build **Passkey-native** mobile applications.

It replaces complex seed phrases with the standard biometrics users already know: **FaceID** or **TouchID**.

## Features
- **Seedless**: Onboard users instantly with Passkeys
- **Gasless**: Sponsored transactions via Paymaster
- **Native**: Built for React Native & Expo
- **Secure**: Hardware-bound credentials

## Installation

```bash
npm install @lazorkit/wallet-mobile-adapter
```

## Usage

```tsx
import { LazorKitProvider, useWallet } from '@lazorkit/wallet-mobile-adapter';
import { View, Button, Text } from 'react-native';

// 1. Wrap App
export default function App() {
  return (
    <LazorKitProvider
      rpcUrl="https://api.devnet.solana.com"
      portalUrl="https://portal.lazor.sh"
      configPaymaster={{ paymasterUrl: "https://lazorkit-paymaster.onrender.com" }}
    >
      <WalletScreen />
    </LazorKitProvider>
  );
}

// 2. Use Hook
function WalletScreen() {
  const { connect, signMessage, isConnected } = useWallet();

  const handleSign = async () => {
    if (!isConnected) {
      await connect({ redirectUrl: 'myapp://home' });
      return;
    }

    const sig = await signMessage("Hello", { 
      redirectUrl: 'myapp://callback' 
    });
    console.log("Signed:", sig);
  };

  return <Button title="Action" onPress={handleSign} />;
}
```

## API Reference

### `useWallet()`

#### `connect(options)`

Connects to the wallet.

**Parameters**

| Param | Type | Description |
|---|---|---|
| `options.redirectUrl` | `string` | Deep link URL |

#### `disconnect()`

Disconnects the wallet.

#### `signMessage(message, options)`

Signs a message string.

**Parameters**

| Param | Type | Description |
|---|---|---|
| `message` | `string` | Content to sign |
| `options.redirectUrl` | `string` | Deep link URL |

**Returns**
`Promise<string>` - Signature

#### `signAndSendTransaction(payload, options)`

Signs and sends transaction.

**Parameters**

| Param | Type | Description |
|---|---|---|
| `payload.instructions` | `TransactionInstruction[]` | Instructions |
| `payload.transactionOptions` | `object` | Config options |
| `transactionOptions.feeToken` | `string` | Token address for gas fees (e.g. USDC). |
| `transactionOptions.computeUnitLimit` | `number` | Max compute units. |
| `transactionOptions.addressLookupTableAccounts` | `AddressLookupTableAccount[]` | Lookup tables for v0 txs. |
| `transactionOptions.clusterSimulation` | `'devnet' \| 'mainnet'` | Network for simulation. |

| `options.redirectUrl` | `string` | Deep link URL |

**Returns**
`Promise<string>` - Signature

## Professional Mobile Wallet Starter

If you want to ship a **production-style seedless + gasless mobile wallet**, use the complete starter screen in:

- `examples/ProfessionalSeedlessGaslessWallet.tsx`

It includes:
- Passkey-based connect/disconnect flow
- Live SOL balance refresh
- Gasless transfer execution via Paymaster
- Defensive UI states for loading/signing/errors

```tsx
import ProfessionalSeedlessGaslessWallet from './examples/ProfessionalSeedlessGaslessWallet';

export default ProfessionalSeedlessGaslessWallet;
```

> Replace `prowallet://home` and `prowallet://callback` with your app deep links and set your own paymaster endpoint for mainnet deployments.


### How to make it work (step-by-step)

1. **Install dependencies in your React Native / Expo app**
   ```bash
   npm install @lazorkit/wallet-mobile-adapter @coral-xyz/anchor react-native-get-random-values
   ```
2. **Copy** `examples/ProfessionalSeedlessGaslessWallet.tsx` into your app codebase (for example `src/screens/WalletScreen.tsx`).
3. **Use your own deep-link scheme** in the example constants:
   - `REDIRECT_HOME`
   - `REDIRECT_SIGN`
4. **Register the same deep-link scheme in your app config** (Expo example):
   ```json
   {
     "expo": {
       "scheme": "prowallet"
     }
   }
   ```
5. **Set environment-specific endpoints** before production:
   - `DEVNET_RPC_URL` → your preferred Solana RPC
   - `PAYMASTER_URL` → your managed paymaster service
   - `PORTAL_URL` → your Lazor portal/project endpoint
6. **Run your app on a real mobile device** (passkeys and deep links are device/browser-flow dependent).

If connect/signing opens a browser but never returns, the deep-link scheme in your code and app configuration do not match exactly.

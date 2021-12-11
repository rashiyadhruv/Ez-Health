<script lang="ts">
  import { onMount } from "svelte";
  import { TezosToolkit } from "@taquito/taquito";
  import type { ContractAbstraction, Wallet } from "@taquito/taquito";
  import { BeaconWallet } from "@taquito/beacon-wallet";
  import { NetworkType } from "@airgap/beacon-sdk";

  let Tezos: TezosToolkit;
  let wallet: BeaconWallet;
  let userAddress = "";
  let loadingProfile = true;

  const rpcUrl = "https://api.tez.ie/rpc/florencenet";

  const connect = async () => {
    try {
      wallet = new BeaconWallet({
        name: "EZ-EMERGENCY",
        preferredNetwork: NetworkType.FLORENCENET,
      });
      await wallet.requestPermissions({
        network: {
          type: NetworkType.FLORENCENET,
          rpcUrl,
        },
      });
      Tezos.setWalletProvider(wallet);
      userAddress = await wallet.getPKH();
    } catch (err) {
      console.error(err);
    } finally {
      loadingProfile = false;
    }
  };
  const disconnect = () => {
    wallet.client.destroy();
    wallet = undefined;
    userAddress = "";
  };

  onMount(async () => {
    Tezos = new TezosToolkit(rpcUrl);
    wallet = new BeaconWallet({
      name: "EZ-EMERGENCY",
      preferredNetwork: NetworkType.FLORENCENET,
    });
    const activeAccount = await wallet.client.getActiveAccount();

    if (activeAccount) {
      Tezos.setWalletProvider(wallet);
      userAddress = activeAccount.address;
      loadingProfile = false;
    }
  });
</script>

<main>
  <div class="container">
    <p>HELLO</p>
  </div>
</main>

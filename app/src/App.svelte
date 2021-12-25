<script lang="ts">
  import { onMount } from "svelte";
  import { TezosToolkit } from "@taquito/taquito";
  import type { ContractAbstraction, Wallet } from "@taquito/taquito";
  import { BeaconWallet } from "@taquito/beacon-wallet";
  import { NetworkType } from "@airgap/beacon-sdk";
  import { Router, Link, Route } from "svelte-routing";

  // pages
  import Home from "./pages/Home.svelte";
  import About from "./pages/About.svelte";

  export let url = "";
  let Tezos: TezosToolkit;
  let wallet: BeaconWallet;
  let userAddress = "";
  let loadingProfile = true;

  const rpcUrl = "https://api.tez.ie/rpc/florencenet";

  const connect = async () => {
    const Tezos = new TezosToolkit("https://mainnet-tezos.giganode.io");
    const wallet = new BeaconWallet({ name: "Beacon Docs Taquito" });

    Tezos.setWalletProvider(wallet);

    try {
      console.log("Requesting permissions...");
      const permissions = await wallet.client.requestPermissions();
      console.log("Got permissions:", permissions.address);
    } catch (error) {
      console.log("Got error:", error);
    }
  };

  connect();

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
      console.log("ALREADY CONNECTED");
    } else {
      console.log("NOT CONNECTED");
    }
  });
</script>

<Router {url}>
  <nav>
    <Link to="/">Home</Link>
    <Link to="about">About</Link>
  </nav>
  <div>
    <Route path="/"><Home /></Route>
    <Route path="/about"><About /></Route>
  </div>
</Router>

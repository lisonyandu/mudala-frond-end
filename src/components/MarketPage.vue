<template>
  <Toast position="top-right"/>
  <div class="grid">

    <div class="col-12" v-if="walletStore.walletData === null">

      <div class="grid grid-nogutter text-800">
        <div
            class="col-12 md:col-6 text-center md:text-left flex align-items-center"
        >
          <section>

            <div class="">
              <span class="text-4xl font-bold mb-1">How to Trade</span>

            </div>

            <div class="flex flex-column mt-6">
              <p><i class="pi pi-check-circle text-green-800 mr-2"></i> Become a Member</p>
              <p><i class="pi pi-check-circle text-green-800 mr-2"></i> If seller, get validation, request credits and sell on exchange</p>
              <p><i class="pi pi-check-circle text-green-800 mr-2"></i> If buyer, connect wallet and buy credits</p>
            </div>

          </section>
        </div>
        <div
            class="col-12 md:col-6 overflow-hidden flex align-items-center justify-content-center"
        >
          <img
              :src="heroImage()"
              alt="Image"
              class="md:h-25rem md:w-fit"
              style="width: 100%"
          />
        </div>
      </div>

    </div>

    <div class="col-12">
      <div class="flex p-fluid">
        <div class="">
          <!--    :disabled="walletStore.walletData != null"      -->
          <Button
              v-if="walletStore.walletData === null"

              @click="connectWallet"
              class="" :label="getConnectionLabel()" icon="pi pi-wallet" style="width: auto"/>
          <p v-if="walletStore.walletData != null" class="text-xl text-green-800 mr-4"><i class="pi pi-wallet mr-3"></i>{{getConnectionLabel()}}</p>
        </div>

        <div style="min-width: 4rem" v-if="walletStore.walletData != null">
          <Button label="Reset" class="p-button-danger ml-2" icon="pi pi-times" style="width: auto" @click="reset"/>

        </div>
      </div>
    </div>

    <div class="col-12 lg:col-6 xl:col-3 mt-5" v-if="walletStore.walletData != null && membertype != null">
      <div class="card mb-0">
        <div class="flex justify-content-between mb-3">
          <div>
            <span class="block text-500 font-medium mb-3">Account Type</span>
            <div class="text-900 font-medium text-xl">{{ titleCase(membertype) }}</div>
          </div>
          <div class="flex align-items-center justify-content-center bg-orange-100 border-round"
               style="width:2.5rem;height:2.5rem">
            <i class="pi pi-users text-orange-500 text-xl"></i>
          </div>
        </div>
        <span class="text-green-500 font-medium"></span>
        <span class="text-500"></span>
      </div>
    </div>

    <div class="col-12 lg:col-6 xl:col-3 mt-5" v-if="walletStore.walletData != null &&  membertype === 'buyer'">
      <div class="card mb-0">
        <div class="flex justify-content-between mb-3">
          <div>
            <span class="block text-500 font-medium mb-3">Available on Market</span>
            <div class="text-900 font-medium text-xl">{{ marketCCTbalance }}</div>
          </div>
          <div class="flex align-items-center justify-content-center bg-orange-100 border-round"
               style="width:2.5rem;height:2.5rem">
            <i class="pi pi-wallet text-orange-500 text-xl"></i>
          </div>
        </div>
        <span class="text-green-500 font-medium">CCT </span>
        <span class="text-500">Tokens</span>
      </div>
    </div>

    <div class="col-12 lg:col-6 xl:col-3 mt-5" v-if="walletStore.walletData != null">
      <div class="card mb-0">
        <div class="flex justify-content-between mb-3">
          <div>
            <span class="block text-500 font-medium mb-3">Your Balance</span>
            <div class="text-900 font-medium text-xl">{{ myCCTbalance }}</div>
          </div>
          <div class="flex align-items-center justify-content-center bg-orange-100 border-round"
               style="width:2.5rem;height:2.5rem">
            <i class="pi pi-wallet text-orange-500 text-xl"></i>
          </div>
        </div>
        <span class="text-green-500 font-medium">CCT </span>
        <span class="text-500">Tokens</span>
      </div>
    </div>


    <div class="col-12 mt-5" v-if="walletStore.walletData != null && this.membertype ==='buyer'">
      <Dialog header="Buy" v-model:visible="displayBuy" :breakpoints="{'960px': '75vw'}"
              :style="{width: '30vw'}" :modal="true">

        <div class=" p-fluid">
          <div class="col-12">
            <div class="p-inputgroup">
                    <span class="p-inputgroup-addon">
                        <i class="pi pi-user"></i>
                    </span>
              <InputText placeholder="Amount" type="number"
                         v-model="amount"/>
            </div>
          </div>
        </div>

        <template #footer>
          <Button label="Buy" @click="buy" icon="pi pi-check" class="p-button-outlined"/>
        </template>
      </Dialog>
      <Button label="BUY" icon="pi pi-wallet" @click="open('buy')"/>
    </div>

    <div class="col-12 mt-5 " v-if="walletStore.walletData != null && this.membertype ==='seller'">
      <Dialog header="Sell" v-model:visible="displaySell" :breakpoints="{'960px': '75vw'}"
              :style="{width: '30vw'}" :modal="true">

        <div class=" p-fluid">
          <div class="col-12">
            <div class="p-inputgroup">
                    <span class="p-inputgroup-addon">
                        <i class="pi pi-user"></i>
                    </span>
              <InputText placeholder="Amount" type="number"
                         v-model="amount"/>
            </div>
          </div>
        </div>

        <template #footer>
          <Button label="Sell" @click="sell" icon="pi pi-check" class="p-button-outlined"/>
        </template>
      </Dialog>
      <Button label="SELL" icon="pi pi-wallet" @click="open('sell')"/>
    </div>
  </div>
</template>

<script>
// import {useWalletStore} from '@/stores/wallet'
// import axios from "axios";
// import { PeraWalletConnect } from "@perawallet/connect";

//  const walletStore = useWalletStore();
// import {SignerTransaction} from "@perawallet/connect/dist/util/model/peraWalletModels";
  // const peraWallet = new PeraWalletConnect({ chainId: 416002 });
export default {
  props: {
    title: String,
  },
  data() {
    return {
      amount: 0,
      displayBuy: false,
      displaySell: false,
      myCCTbalance: null,
      marketCCTbalance: null,
      membertype: null,
      projectid: null,
      taxid: null,
    }
  },

  setup() {
 
  // const peraWallet = new PeraWalletConnect({ chainId: 416002 });
  
//   const connectWallet = async () => {
  
//     // if(!peraWallet.isConnected)
//     // {  
//       peraWallet
//         .connect()
//         .then((accounts) => {
//           // peraWallet.connector.on("disconnect", this.disconnectWallet);
//           const accountAddress = accounts[0];
//           walletStore.saveWalletData(accountAddress);
//           console.log("The connected account: ",accountAddress);
//           // this.successToast('Success', `You have successfully connected your wallet!`)
//         })
//         .catch((e) => console.log(e));
//     }
    
//   // };
//   const disconnectWallet = async()=> {
//       peraWallet.disconnect().then(() => (this.accountAddress = null));
//     };
//   return {
//     connectWallet,
//     walletStore,
//     disconnectWallet
//   };
// },
// mounted() {
//   // const peraWallet = new PeraWalletConnect({ chainId: 416002 });
//   if (this.walletStore.walletData != null) {
//     this.myAccount(this.walletStore.walletData);
//     this.marketAccount();
//   }
//   peraWallet
//       .reconnectSession()
//       // .then((accounts) => {
//       // peraWallet.connector.on("disconnect", this.disconnectWallet);

   
},
  methods: {
    open(d) {
      if (d === 'sell') this.displaySell = true;
      if (d === 'buy') this.displayBuy = true
    },
    close() {
      this.displaySell = false;
      this.displayBuy = false;
    },
    successToast(s, d) {
      this.$toast.add({
        severity: "success",
        summary: s,
        detail: d,
        life: 4000,
      });
    },
    errorToast(s, d) {
      this.$toast.add({
        severity: "error",
        summary: s,
        detail: d,
        life: 3000,
      });
    },
    heroImage() {
      return this.$appState.darkTheme
          ? "images/img_1.png"
          : "images/img_1.png";
    },
    getConnectionLabel() {
      return this.walletStore.walletData != null ? `${this.walletStore.walletData}` : 'Connect Wallet'
    },
// async buy() {
//   this.close();
//   try {

//     await this.myAccount(useWalletStore().walletData);
//     console.log(requests);
//   } catch (err) {
//     console.error(err);
//     this.errorToast('Error', 'Error purchasing tokens');
//   }

// },
// async sell() {
//   this.close();
//   try {
   
//     await this.myAccount(useWalletStore().walletData);
  
//   } catch (err) {
//     console.error(err);
//     this.errorToast('Error', 'Error selling tokens')
//   }
// },
    // reset() {
    //   useWalletStore().$reset();
    //   console.log('Reset connection to wallet')
    // },
    // titleCase(str) {
    //   return str.charAt(0).toUpperCase() + str.slice(1);
    // },
// async myAccount(wallet) {
//   try {
//     console.log('Getting account balance for wallet:', wallet);

//     this.myCCTbalance = val.balance;

//     axios.post('/member/registrationdata', { walletaddress: wallet })
//       .then(res => {
//         console.log('Registration data response:', res);
//         this.membertype = res.data.membertype;
//         this.projectid = res.data.projectid;
//         this.taxid = res.data.taxid;
//       })
//       .catch(err => {
//         console.log('Registration data error:', err);
//         this.membertype = null;
//       });
//   } catch (e) {
//     console.error('Error retrieving balance:', e);
//     this.errorToast('Error', 'Error retrieving balance');
//   }
// },
  //   async marketAccount() {

  //     try {
  

  //     } catch (e) {
  //       this.errorToast('Error', 'Error retrieving balance')
  //     }
  //   }
  // },
  // computed: {
  //   accAvailable() {
  //     return useWalletStore().walletData
  //   },
  // },
  // watch: {
  //   async accAvailable(newVal, old) {
  //     console.log(`updating from ${old} to ${newVal}`)
  //     if (newVal) {
  //       await this.myAccount(newVal);
  //       await this.marketAccount();
  //     }
  //   },
  },
};
</script>




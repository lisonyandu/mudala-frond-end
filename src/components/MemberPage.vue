<template>
  <Toast position="top-right"/>
  <div class="grid">
    <div class="col-12" v-if="accountAddress == null">
      <div class="grid grid-nogutter text-800">
        <div
            class="col-12 md:col-6 text-center md:text-left flex align-items-center"
        >
          <section>

            <div class="">
              <span class="text-4xl font-bold mb-1">To access your account </span>

            </div>

            <div class="flex flex-column mt-6">
              <p><i class="pi pi-check-circle text-green-800 mr-2"></i> You'll need to connect your wallet first.</p>
          <p><i class="pi pi-check-circle text-green-800 mr-2"></i> This process involves signing a transaction containing zero algos in order to authenticate your account.</p>
          <p><i class="pi pi-check-circle text-green-800 mr-2"></i>  Once your account is confirmed, you'll be able to log in and start using our services.</p>
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
      <div class="grid grid-nogutter text-800">
        <div
            class="col-12 md:col-6 text-center md:text-left flex align-items-center"
        >
          <section>

            <div class="">
              <span class="text-4xl font-bold mb-1">Features</span>

            </div>

            <div class="flex flex-column mt-6">
              <p><i class="pi pi-check-circle text-green-800 mr-2"></i> Check balance</p>
              <p><i class="pi pi-check-circle text-green-800 mr-2"></i> Request credits</p>
              <p><i class="pi pi-check-circle text-green-800 mr-2"></i> Manage your account</p>
              <a class="pi pi-check-circle text-green-800 mr-2"  href="https://testnet.explorer.perawallet.app/assets/212175420/"> Click here to Opt-in https://testnet.explorer.perawallet.app/assets/212175420/ </a>
            </div>

          </section>
        </div>
        <div
            class="col-12 md:col-6 overflow-hidden flex align-items-center justify-content-center"
        >

        </div>
      </div>

    </div>
    <!-- <button @click="disconnectWallet" v-if="accountAddress">Disconnect</button> -->
  <!-- <button @click="connectWallet" v-else>Connect to Pera Wallet</button> -->
  <div v-if="accountAddress" class="text-xl text-green-800 mr-4"><i class="pi pi-wallet mr-3"> </i>{{getConnectionLabel()}}</div>
  <div style="min-width: 4rem" v-if="accountAddress != null">
          <Button label="Reset" class="p-button-danger ml-2" icon="pi pi-times" style="width: auto" @click="resetAndDisconnectWallet"/>

        </div>
 
    <div class="col-12">
      <div class="grid p-fluid ">
        <!--          <div class="col-12 md:col-3"></div>-->
        <!-- <div class="col-12 md:col-3">
          <div class="p-inputgroup">
                    <span class="p-inputgroup-addon">
                        <i class="pi pi-user"></i>
                    </span>
    
          </div>
        </div> -->

        <div v-if="accountAddress == null" class="col-12 md:col-3">
          <Button
              label="My Account"
              icon="pi pi-wallet"
              class="ml-2"
              style="width: auto"
              @click="connectWallet"
          ></Button>
        </div>
      </div>
    </div>

    <div class="col-12 lg:col-6 xl:col-3" v-if= "dataloaded && accountAddress != null">
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

    <div class="col-12 lg:col-6 xl:col-3" v-if="dataloaded && accountAddress != null">
      <div class="card mb-0">
        <div class="flex justify-content-between mb-3">
          <div>
            <span class="block text-500 font-medium mb-3">Balance</span>
            <div class="text-900 font-medium text-xl">{{ accountbalance }}</div>
            
          </div>
          <div class="flex align-items-center justify-content-center bg-blue-100 border-round"
               style="width:2.5rem;height:2.5rem">
            <i class="pi pi-wallet text-blue-500 text-xl"></i>
          </div>
        </div>
        <span class="text-green-500 font-medium">CCT </span>
        <!--        <span class="text-500">Tokens</span>-->
      </div>
    </div>
    <div class="col-12 lg:col-6 xl:col-3" v-if="dataloaded"></div>

    <div class="col-12 md:col-3 flex justify-content-end align-items-end" v-if="dataloaded && accountAddress != null">
      <div v-if="membertype === 'seller'">
        <Dialog header="Request Form"
                v-model:visible="display"
                :breakpoints="{'960px': '75vw'}"
                :style="{width: '30vw'}" :modal="true">

          <div class=" p-fluid">
            <div class="col-12">
              <div class="p-inputgroup">
                    <span class="p-inputgroup-addon">
                        <i class="pi pi-user"></i>
                    </span>
                <InputText placeholder="Member ID"
                           type="number"
                           v-model="memberid"/>
              </div>
            </div>
          </div>

          <template #footer>
            <Button label="Request" @click="sendRequest(memberid)" icon="pi pi-check" class="p-button-outlined"/>
          </template>
        </Dialog>
        <Button label="Request Credits" icon="pi pi-external-link" style="width: auto" @click="open"/>
      </div>
    </div>


    <div class="col-12" v-if="dataloaded && membertype === 'seller' && accountAddress != null ">
      <div class="">

        <div class="mt-5">
          <DataTable :value="requests">
            <template #header> Credit Requests</template>
            <Column
                v-for="col of requestsColumns"
                :field="col.field"
                :header="col.header"
                :key="col.field"
            >
              <template #body="slotProps" v-if="col.field === 'status'">
                <span :class="'customer-badge status-'+ slotProps.data.status">{{ slotProps.data.status }}</span>
              </template>
            </Column>

            <Column headerStyle="width:5rem">
              <template #body="slotProps">
                <div class="grid" style="min-width: 5rem;">

                  <Button class="p-button-danger p-button-icon-only ml-1" icon="pi pi-trash"
                          @click="deleteRequest(slotProps.data)"/>
                </div>
              </template>
            </Column>
          </DataTable>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { PeraWalletConnect } from "@perawallet/connect";
import {useWalletStore} from '@/stores/wallet'
const peraWallet = new PeraWalletConnect({ chainId: 416002 });
 const walletStore = useWalletStore();
//  const algosdk = require('algosdk');
// You must sign a 0 ALGO transaction to prove ownership of this address.
// This transaction does not get published to the network,
// it is only used to verify account ownership on AlgoExplorer.


export default {
  props: {
    title: String,
  },
  data() {
    return {
      display: false,
      memberid: null,
      membertype: null,
      projectid: null,
      taxid: null,
      walletaddress: null,
      amount: null,
      accountbalance: null,
      dataloaded: false,
      requests: null,
      requestsColumns: null,
      accountAddress :null

    }
  },
  mounted() {
    this.myRequests(this.memberid)
    peraWallet
      .reconnectSession()
      .then((accounts) => {
        peraWallet.connector.on("disconnect", this.disconnectWallet);

        if (accounts.length) {
          this.accountAddress = accounts[0];
        }
      })
      .catch((error) => {
        if (error?.data?.type !== "CONNECT_MODAL_CLOSED") {
          console.log(error);
        }
      });
  },
  methods: {
    async connectWallet() {
      peraWallet
        .connect()
        .then((accounts) => {
          peraWallet.connector.on("disconnect", this.disconnectWallet);

          this.accountAddress = accounts[0];
          walletStore.saveWalletData(this.accountAddress);
          this.authenticate()
        })
        .catch((e) => console.log(e));
    },
    async disconnectWallet() {
      peraWallet.disconnect().then(() => (this.accountAddress = null));
    },



//     async authenticate() {
//     if (!this.accountAddress) {
//         console.error("Wallet not connected");
//         return;
//     }

//     console.log("Do we at least get to this point");

//     try {
//         // Send a request to your backend to get the transaction data
//         const response = await axios.post("/api/authenticate", {
//             accountAddress: this.accountAddress,
//         });

//        // Zero-Algo Case:  Use bytes from the first (and only) array element
//         const encodedTransaction  = response.data.txn; // Assuming Uint8Array 
//         console.log("Is this what we have been missing:? ",encodedTransaction);
//         const rawTransactionBytes = new Uint8Array(Object.values(encodedTransaction)); 
//         console.log("Does Pera accept this? : ",rawTransactionBytes);
//         // const txnData = response.data; // Convert to JSON string
//         // console.log("This is the response from backend", txnData);
       
//         const txnGroup = [{ txn: rawTransactionBytes, signers: [this.accountAddress] }];

//         console.log("Constructed txn: ",txnGroup);
//         // Sign the transaction using PeraWallet
//         const signedTxnArray = await peraWallet.signTransaction([txnGroup]);

//         console.log("Signed?? txn: ",signedTxnArray);
//         const signedTxn = algosdk.decodeSignedTransaction(signedTxnArray[0]);

//         // Submit the signed transaction to the backend
//         const backendResponse = await axios.post("/api/submitTransaction", {
//             signedTxn,
//         });

//         // Handle success from the backend
//         console.log("Backend response:", backendResponse.data);
//     } catch (error) {
//         // Handle errors from both frontend and backend
//         console.error("Authentication error:", error);
//     }
// },


    resetAndDisconnectWallet() {
      // Disconnect the wallet
      this.disconnectWallet();
    },
    getConnectionLabel() {
      return this.accountAddress != null ? `Connected to: ${this.accountAddress}` : 'Connect Wallet'
    },
    open() {
      this.display = true;
    },
    close() {
      this.display = false;
    },
    titleCase(str) {
      return str.charAt(0).toUpperCase() + str.slice(1);
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
    sendRequest(memberid) {
      const n = new Date();
      axios.post('/member/requestcredit', {
            memberid: memberid,
            date: `${n.getDate()}/${(n.getMonth() + 1)}/${n.getFullYear()}`
          }
      ).then(() => {
        // this.memberid = res.data.memberid;
        this.dataLoaded = true;
        this.successToast('Success', `Request successful`)
        this.myAccount(this.walletaddress);
        this.close();
      }).catch(err => {
        console.log(err)
        this.close();
        this.errorToast('Error', err.response.data)
      })
    },
    
//   authenticateBackend(accountAddress) {
//   // Send a POST request to your backend authentication endpoint
//   axios.post('/api/authenticate', { accountAddress })
//     .then((response) => {
//       console.log(response.data.message);
//       this.successToast('Success', 'Your identity has been confirmed!');
//       this.myAccount(accountAddress);
//     })
//     .catch((error) => {
//       console.error(error);
//       this.errorToast('Error', 'Authentication failed. Please try again.');
//     });
// },
    myAccount(walletaddress) {
      axios.post('/api/myaccount', {
          walletaddress: walletaddress
          }
      ).then(res => {
        this.accountbalance = res.data.balance;
        this.membertype = res.data.membertype;
        this.projectid = res.data.projectid;
        this.taxid = res.data.taxid;
        this.memberid = res.data.memberid;
        this.walletaddress = res.data.walletaddress;
        this.dataloaded = true;
        this.myRequests(res.data.memberid);
      }).catch(err => {
        console.log(err.message)
        this.errorToast('Error', `Account not found`)
      })
      // this.myRequests(this.memberid);
    },
    myRequests(memberid) {
      axios.post("/member/myrequests", {
            memberid: memberid
          }
      ).then((res) => {
        this.requests = res.data;
        this.requestsColumns = [
          {field: "code", header: "#"},
          {field: "memberid", header: "Member ID"},
          {field: "companyname", header: "Member"},
          {field: "wallet", header: "Wallet"},
          {field: "date", header: "Date"},
          {field: "status", header: "Status"},
        ];
      }).catch(e => {
        console.log(e.message);
      });
    },
    deleteRequest(data) {
      axios.post('/member/delete',
          {
            memberid: data.memberid,
            code: data.code
          }
      ).then(() => {
        this.myRequests(data.memberid);
        this.successToast("Success", `You deleted the request`);
      }).catch(err => {
        console.log(err)
        this.errorToast("Error", `An error occurred`);
      })
    }
  }
};


</script>

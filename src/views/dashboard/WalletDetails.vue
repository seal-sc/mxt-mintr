<template>
  <div class="wallet-div">

    <div class="row">
      <div class="col text-left wallet-detail-tit">{{ $t("dashboard.wallet.details") }}</div>
      <div class="col">
<!--        <input type="submit" class="btn btn-info btn-refresh" value="Refresh">-->
      </div>
    </div>

    <div class="row wallet-div ratio-div">
      <div class="col">
        <div class="row">
          <div class="col text-center txt-ratio-tit">{{mintingRate * 100}} %</div>
        </div>
        <div class="row">
          <div class="col text-center txt-ratio-txt">{{ $t("dashboard.wallet.ratio") }}</div>
        </div>
      </div>
    </div>

    <div class="row total-token-div">
      <div class="col">
        <div class="row">
          <div class="col text-left token-price">{{ $t("dashboard.wallet.total") }} {{ $t("token.name") }}:</div>
          <div class="col text-right token-price">{{totalTOKEN}} {{ $t("token.name") }}</div>
        </div>
        <hr class="total-hr">
        <div class="row">
          <div class="col text-left lock-tran-txt">
            {{ $t("dashboard.wallet.locked") }} {{lockedTOKEN}}
          </div>
          <div class="col text-right lock-tran-txt">
            {{ $t("dashboard.wallet.transfer") }} {{tokenBalance}}
          </div>
        </div>
        <div class="row">
          <div class="col lock-tran-color-1"></div>
          <div class="col lock-tran-color-2"></div>
        </div>
      </div>
    </div>

    <div class="row balance-list-div">
      <div class="col">
        <div class="row">
          <div class="col text-center token-price">{{ $t("dashboard.wallet.balanceList") }}</div>
        </div>
        <div class="row balance-item-1">
          <div class="col">
            <img class="balance-logo" :src="'/static/logo/icon.png'" alt=""/>
            {{ $t("token.name") }}
          </div>
          <div class="col text-right">{{tokenBalance}}</div>
        </div>
        <div class="row balance-item-2">
          <div class="col">
            <img class="balance-logo" :src="'/static/token/ETH.svg'" alt=""/>
            {{ $t("token.eth") }}
          </div>
          <div class="col text-right">{{ethBalance}}</div>
        </div>
        <div class="row balance-item-1">
          <div class="col">
            <img class="balance-logo" :src="'/static/logo/icon.png'" alt=""/>
            {{ $t("token.synthAsset") }}
          </div>
          <div class="col text-right">{{synBalance}}</div>
        </div>
        <div class="row balance-item-2">
          <div class="col">
            <img class="balance-logo" :src="'/static/logo/icon.png'" alt=""/>
            {{ $t("token.lockedToken") }}
          </div>
          <div class="col text-right">{{lockedTOKEN}}</div>
        </div>
      </div>
    </div>

    <div class="row go-to-uniswap-btn">
      <div class="col">
        <a
          class="go-to-uniswap-txt"
          :href="$t('links.uniswap')" target="_blank"
        >{{ $t("dashboard.wallet.uniswap") }}</a
        >
      </div>
    </div>

    <loading :active.sync="loading"
             :can-cancel="false"
             :is-full-page="true"></loading>
  </div>
</template>

<script>
  import mxt from "../../utils/mxt/mxt";
  import {syntheticAddr} from "../../utils/mxt/constant";
  import Decimal from "decimal.js"
  // Import component
  import Loading from 'vue-loading-overlay';
  // Import stylesheet
  import 'vue-loading-overlay/dist/vue-loading.css';

  let opt = mxt.opt
  let querying = false

  async function commonBalance(balanceFunc, addr) {
    return await balanceFunc(addr)
            .then(r=>{
              if(r === null) {
                return "0"
              }
              return r
            })
  }

  export default {
    name: "WalletDetails",

    components: {
      Loading
    },

    mounted() {
      setInterval(async _=>{
        if(querying) {
          return
        }

        querying = true

        this.tokenBalance = await commonBalance(opt.tokenBalance)
        this.ethBalance = await commonBalance(opt.ethBalance)
        this.mintingRate = await commonBalance(opt.currentMintingRate, syntheticAddr)
        this.synBalance = await commonBalance(opt.synAssetsBalance, syntheticAddr)
        this.lockedTOKEN = await commonBalance(opt.lockedTOKENFor, syntheticAddr)

        this.loading = false
        querying = false
      }, 1000)
    },

    data() {
      return {
        mintingRate: "0",
        tokenBalance: "0",
        ethBalance: "0",
        synBalance: "0",
        lockedTOKEN: "0",
        loading: true,
      }
    },

    computed: {
      totalTOKEN() {
        return new Decimal(this.tokenBalance).add(this.lockedTOKEN).toDP(6, Decimal.ROUND_DOWN)
      }
    },

    methods: {
      // get
      async getRatio() {
        return opt.currentMintingRate(syntheticAddr)
      },

      async getBalance() {

      }
    }
  }
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
.wallet-div,
.ratio-div,
.fee-div,
.total-token-div,
.balance-list-div {
  border-width: 1px;
  border-style: solid;
  border-color: rgb(0, 167, 230);
  border-image: initial;
  border-radius: 5px;
  padding: 32px 24px;
}
.ratio-div,
.fee-div,
.total-token-div,
.balance-list-div {
  margin-top: 24px !important;
  padding: 24px 24px;
}
.wallet-detail-tit {
  color: rgb(255, 255, 255);
  letter-spacing: 1px;
  font-family: apercu-medium, PingFang SC, 'Avenir', Helvetica, Arial, sans-serif;
  font-weight: 500;
  display: flex;
  align-items: center;
  height: 40px;
}
.btn-refresh {
  background-color: transparent;
  height: 40px;
  display: flex;
  -webkit-box-align: center;
  align-items: center;
  -webkit-box-pack: justify;
  justify-content: space-between;
  font-size: 14px;
  cursor: pointer;
  border-width: 1px;
  border-style: solid;
  border-color: rgb(0, 167, 230);
  border-image: initial;
  padding: 2px 20px 0px;
  border-radius: 20px;
  transition: all 0.1s ease-in 0s;
  text-decoration: none;
  color: #c2c2de;
  float: right;
}
.txt-ratio-tit {
  font-size: 24px;
  color: rgb(194, 193, 225);
  line-height: 16px;
  letter-spacing: 1px;
  font-family: apercu-medium, PingFang SC, 'Avenir', Helvetica, Arial, sans-serif;
  font-weight: 500;
  margin: 0 0 8px;
}
.txt-ratio-txt {
  font-size: 18px;
}
.token-logo {
  height: 24px;
}
.token-price,
.lock-tran-txt {
  margin-left: 8px !important;
  font-size: 16px;
  font-family: apercu-medium, sans-serif;
  font-weight: bold;
  -webkit-box-align: center;
  align-items: center;
  color: rgb(194, 193, 225);
}
.total-hr {
  border-bottom: 1px solid rgb(0, 167, 230);
}
.lock-tran-txt {
  font-size: 14px;
}
.lock-tran-color-1 {
  margin-top: 8px !important;
  height:20px;
  background:rgba(104,101,163,1);
}
.lock-tran-color-2 {
  margin-top: 8px !important;
  height:20px;
  background:rgba(58,59,116,1);
}

.balance-item-1,
.balance-item-2 {
  margin-top: 8px !important;
  font-family: "apercu-regular", PingFang SC, 'Avenir', Helvetica, Arial, sans-serif;
  font-size: 14px;
  padding-top: 4px;
  padding-bottom: 4px;
}
.balance-item-1 {
  background-color: #1c1a27;
}
.balance-logo {
  height: 14px;
  margin-right: 4px;
}

.go-to-uniswap-btn {
  background-color: rgb(28, 26, 40);
  text-transform: uppercase;
  cursor: pointer;
  width: 100%;
  text-align: center;
  text-decoration: none;
  padding: 16px 20px;
  border-width: 1px;
  border-style: solid;
  border-color: rgb(0, 167, 230);
  border-image: initial;
  border-radius: 2px;
  margin-top: 24px !important;
}
.go-to-uniswap-txt {
  color: rgb(194, 193, 225);
}

</style>

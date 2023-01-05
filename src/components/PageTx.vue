<template lang="pug">
tm-page(:title="`Transaction ${hash}`")
  div(slot="menu"): tm-tool-bar
    router-link(:to="`/blocks/${height}`")
      i.material-icons chevron_left
      | Block {{ height }}
    a(:href="jsonUrl" target="_blank") JSON

  div(v-if="decodedTx")
    part-tx-data(
      :data="decodedTx"
      name="Transaction Details"
      pathPrefix="tx."
      :includeFields="[]"
      :excludeFields="[]"
    )
  tm-part(v-else title="Transaction was not found")
</template>

<script>
import { mapGetters } from "vuex"
import axios from "axios"
import { decodeTx } from "../scripts/tx"
import PartTxData from './PartTxData'
import { TmListItem, TmPage, TmPart, TmToolBar } from "@tendermint/ui"

export default {
  name: "page-tx",
  components: {
    TmToolBar,
    TmListItem,
    TmPart,
    TmPage,
    PartTxData
  },
  computed: {
    ...mapGetters(["blockchain"]),

    hash() {
      return this.$route.params.hash
    },
    prevHeight() {
      return this.block.header.height - 1
    },
    nextHeight() {
      return this.block.header.height + 1
    },
    decodedTx () {
      let { tx, height } = this
      if (!tx) return

      let txObj = tx;
      let txHash = this.hash
      let block = {
        isRouterLink: true,
        title: "View block details",
        text: height,
        to: { name: "block", params: { block: height } }
      }
      return Object.assign({ block, txHash }, txObj)
    },
  },
  data: () => ({
    jsonUrl: "",
    // hash: this.$route.params.hash,
    tx: void 0,
    height: "",
  }),
  methods: {
    async fetchTx() {
      console.log(this.hash)
      this.jsonUrl = `${this.blockchain.rpc}/tx?hash=0x${this.hash}`
      console.log(this.jsonUrl)
      let json = await axios.get(this.jsonUrl)
      console.log(json.data.result)
      this.height = json.data.result.height
      console.log(json.data.result.tx)
      this.tx = json.data.result
    },
  },
  async mounted() {
    await this.fetchTx()
  },
  watch: {
    // eslint-disable-next-line
    '$route'(to, from) {
      this.fetchTx()
    }
  }
}
</script>

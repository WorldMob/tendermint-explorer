<template lang="pug">
tm-page(title='Full Nodes')
  tm-part(title='Search for node')
    tm-form-struct(:submit="getNodes")
      tm-form-group
        .tm-modal-search
          tm-field#search-input(
            type="text"
            placeholder="Search by moniker"
            required
            v-model="query"
            title="1 to 60 characters")
          tm-btn(type="button" icon="search" value="Go")

  //tm-part(title='Filters')
  //a(@click.prevent='toggleFilter'): i.material-icons(:class="{'mdi-rotate-180': asc}") filter_list

  tm-part(title='Nodes')
    tm-list-item(
      v-for="(item,item_index) in getNodes"
      :key="item"
      :title="item.node_info.moniker"
      :subtitle="getIp(item)"
      icon='storage'
      :to="`/nodes/${urlsafeIp(getIp(item))}`")



</template>

<script>
import { mapGetters } from "vuex"
import { orderBy } from "lodash"
import {
  TmListItem,
  TmFormGroup,
  TmFormStruct,
  TmToolBar,
  TmTabBar,
  TmPage,
  TmPart,
  TmBtn,
  TmField
} from "@tendermint/ui"
export default {
  name: "page-nodes",
  components: {
    TmBtn,
    TmField,
    TmFormGroup,
    TmFormStruct,
    TmPage,
    TmPart,
    TmListItem,
    TmPage,
    TmTabBar,
    TmToolBar
  },
  data() {
    return {
      asc: true,
      nodeslist: [],
      query: ""
    }
  },
  computed: {
    ...mapGetters(["nodes"]),

    getNodes() {
      if (this.nodes) {
        let nodeslist = this.nodes;

        nodeslist = orderBy(
          nodeslist,
          [n => n.node_info.moniker.toLowerCase()],
          this.asc ? "asc" : "desc"
        )

        if (this.query) {
          nodeslist = this.search(nodeslist)
        }

        return nodeslist
      } else {
        return []
      }
    }
  },
  methods: {
    toggleFilter() {
      this.asc = !this.asc
    },
    search(nodeslist) {
      if (this.query) {
        let tempNodesList = nodeslist
        let lowerCaseQuery = this.query.toLowerCase()
        tempNodesList = tempNodesList.filter(n => n.node_info.moniker && n.node_info.moniker.toLowerCase().includes(lowerCaseQuery))
        return tempNodesList;
      }
    },
    urlsafeIp(ip) {
      return ip && ip.split(".").join("-")
    },
    getIp(fullNode) {
      return (
        fullNode.node_info.listen_addr &&
        fullNode.node_info.listen_addr.split(":")[0]
      )
    }
  }
}
</script>
